# Check values:
db.movies.find()

# Insert One and many data:
db.movies.insertOne(data)
db.movies.insertMany([data])

# Comparison query operator: $eq, $lt, $lte, $gt, $gte, $ne, $in, $nin
db.movies.find({
    year: {$lt: 2010}
})

- $in (Matches Any in Array)
# Find movies from specific years:
db.movies.find({year: {$in: [1994, 1999, 2019]}})

- $nin (Matches None in Array)
db.movies.find({year: {$nin: [1994, 2019]}})

# Count:
db.movies.countDocuments({
    year: {$gt: 2010}
})

# Projection:
- Projection is the process of selecting specific fields to include or exclude from query results. Instead of returning entire documents, you can return only the data you need, making queries faster and more efficient.

db.movies.find(
    {year: {$lt: 2000}},
    {title:1, imdb: 1}
)

db.movies.find(
    {year: {$in: [2000, 2010, 2015]}},
    {title: 1, imdb: 1}
)



# Logical operators: $and, $or, $not, and $nor.

- and:

// Find Action movies from 2000s with rating > 8.7
db.movies.find({
  $and: [
    { genres: "Action" },
    { year: { $gte: 2000, $lte: 2009 } },
    { "imdb.rating": { $gt: 8.7 } }
  ]
})


- or:

// Find movies that are either very long OR very highly rated
db.movies.find({
  $or: [
    { runtime: { $gt: 180 } },
    { "imdb.rating": { $gte: 9.0 } }
  ]
})