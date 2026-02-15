# Check values:
db.movies.find()

# Insert One and many data:
db.movies.insertOne(data)
db.movies.insertMany([data])


# Delete one and many:
db.movies.deleteOne({runtime: {$gt: 150}})
db.movies.deleteMany({runtime: {$gt: 150}})


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
db.movies.countDocuments()

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

db.movies.find(
  {
    $and: [
      {year: {$gt: 2010}},
      {"imdb.rating": {$gte: 8}}
    ]
  },
  {title: 1, year: 1}
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


- $or with $and combination:

// Find movies that are (Action AND from 2000s) OR (Drama AND highly rated)
db.movies.find({
  $or: [
    {
      $and: [
        { genres: "Action" },
        { year: { $gte: 2000 } }
      ]
    },
    {
      $and: [
        { genres: "Drama" },
        { "imdb.rating": { $gte: 8.8 } }
      ]
    }
  ]
})



# Simple Memory Aid:

AND = "Give me everything that matches ALL these rules"

OR = "Give me everything that matches ANY of these rules"

NOT = "Give me everything that DOESN'T match this rule"

NOR = "Give me everything that matches NONE of these rules"






# Sorting:

db.movies.find(
  {
    $and: [
      {year: {$gt: 2010}},
      {"imdb.rating": {$gte: 8}}
    ]
  },
  {title: 1, year: 1}
).sort({year: 1})



# Limit: how much data we want to see

db.movies.find(
  {
    $and: [
      {year: {$gt: 2010}},
      {"imdb.rating": {$gte: 8}}
    ]
  },
  {title: 1, year: 1}
).sort({year: 1}).limit(2)




# Distinct: check unique values

db.movies.distinct("year")

output:
[
  1972, 1979, 1980, 1990,
  1991, 1994, 1995, 1998,
  1999, 2000, 2003, 2006,
  2008, 2010, 2014, 2018,
  2019
]


# check length:
db.movies.distinct("year").length // 17



____________________________________________________________________________________________


# MongoDB Aggregation:

1️⃣ What is Aggregation?
Aggregation is the process of performing calculations and transformations on multiple documents to return computed results. It's similar to GROUP BY in SQL but much more powerful.

Why Use Aggregation?

- Group and summarize data
- Perform calculations (sum, average, min, max)
- Transform document structure
- Join collections
- Analyze data trends

# Group by:
db.movies.aggregate([
  { $match: { year: {$gt: 2000} } },
  { $group: { _id: "$genres", avgRating: { $avg: "$imdb.rating" } } }
]).sort({avgRating: 1})




2️⃣ Aggregation Pipeline Concept:
Think of aggregation like an assembly line in a factory:

Stage 1  →  Stage 2  →  Stage 3  →  Stage 4  →  Result
($match)    ($group)    ($sort)     ($project)
- Each stage transforms the data and passes it to the next stage.


db.movies.aggregate([
  // Stage 1: Filter only R-rated movies
  { $match: { rated: "R" } },
  
  // Stage 2: Group by year and calculate average rating
  { $group: { 
    _id: "$year", 
    avgRating: { $avg: "$imdb.rating" },
    count: { $sum: 1 }
  } },

  // Stage 3: Sort by year
  { $sort: {_id: 1} },

  // Stage 4: Format the output
  { $project: {
    year: "$_id",
    avgRating: { $round: ["$avgRating", 1] },
    movieCount: "$count"
  } }
])