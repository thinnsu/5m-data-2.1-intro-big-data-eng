# Assignment

## Brief

Write the Python codes for the following questions.

## Instructions

Paste the answer as Python in the answer code section below each question.

### Question 1

Question: From the `movies` collection, return the documents with the `plot` that starts with `"war"` in acending order of released date, print only title, plot and released fields. Limit the result to 5.

Answer:

```python
for m in movies.find({"plot": {"$regex": "^war", "$options": "i"}}, 
                        {"_id": 0, "title": 1, "plot": 1, "released": 1}).sort('released').limit(5):
    print(f"[Plot] : {m['plot']} [Title] : {m['title']} was released in {m['released']}")
```

### Question 2

Question: Group by `rated` and count the number of movies in each.

Answer:

```python
stage_group_rate = {
   "$group": {
         "_id": "$rated", # Group by the 'rated' field
         # Count the number of movies in the group:
         "movie_count": { "$sum": 1 }, #sum 1 is defult for group by
   }
}
sort_group_rate = {
      "$sort": {
         "_id": pymongo.ASCENDING
      }
   }
pipeline = [
   stage_group_rate,sort_group_rate
]
results = movies.aggregate(pipeline)

# Loop through the 'year-summary' documents:
for year_summary in results:
   print(year_summary)
```

### Question 3

Question: Count the number of movies with 3 comments or more.

Answer:

```python
pipeline = [
    {
        "$lookup": {
            "from": "comments",  # Join with the 'comments' collection
            "localField": "_id",  # Match movie's _id
            "foreignField": "movie_id",  # Match comments' movie_id
            "as": "related_comments"  # Output array of related comments
        }
    },
    {
        "$addFields": {
            "comment_count": { "$size": "$related_comments" }  # Add a field for the count of comments
        }
    },
    {
        "$match": {
            "comment_count": { "$gte": 3 }  # Filter movies with 3 or more comments
        }
    },
    {
        "$count": "movies_with_3_or_more_comments"  # Count the number of such movies
    }
]

# Execute the aggregation pipeline
result = movies.aggregate(pipeline)

# Print the result
for doc in result:
    print(doc)
```

## Submission

- Submit the URL of the GitHub Repository that contains your work to NTU black board.
- Should you reference the work of your classmate(s) or online resources, give them credit by adding either the name of your classmate or URL.
