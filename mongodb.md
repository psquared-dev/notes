To list all the dbs:

```js
show dbs
```

---

To create the collection, the second parameter is used to sepcify configs for e.g capped settings or schema:

```js
db.createCollection("name")
```

---

To drop the database:

```js
db.drop()
```

To drop the collection:

```js
db.collectionName.drop()
```

---

To show all the collections in the current db:

```js
show collections
```

---

To view the stats like total size of collection, index size etc

```js
db.collectionName.stats()
```

## ObjectId

`ObjectId` values are 12 bytes of length

- first 4 bytes seconds since unix epoch
- next 5 bytes are a random number
- last 3 bytes are random counter number

`ObjectId("652bf764fdcc4a468d11146c").getTimestamp()` - to get the associated timestamp

----

## insertOne() and insertMany()

```js
db.collectionName.insertOne(doc) - to insert the doc into the collection
db.collectionName.insertMany([doc1, doc2, doc3]) - to insertan array of docs into the collection
```

There also exists a method called `insert()` which can be used to insert one or more docs, however, it is now deprecated. An important difference between `insert()` and other methods is that, `insert()` doesn't return the `_id` after inserting the docs into the database.

## Changing the behavior of insertOne() and insertMany()

Consider the following command:

```js
db.hobbies.insertMany([
{ _id: "yoga", name: "Yoga" }, 
{ _id: "cooking", name: "Cooking" }, 
{ _id: "hiking", name: "Hiking" },
])
```

Assuming `_id : cooking` already exists the db, the above query would insert the doc `{_id: "yoga", name: "Yoga"}` and then throws and error without entering any other doc in the command.

We can change this behavior using the second argument:

```js
db.hobbies.insertMany(
  [
    { _id: "yoga", name: "Yoga" },
    { _id: "cooking", name: "Cooking" },
    { _id: "hiking", name: "Hiking" },
  ],
  { ordered: false }
);
```

Now all the docs with duplicate IDs are skipped but the remaining docs are inserted into the db.

**writeConcern**

`{w: 0}` - don't wait for server acknowledgement.
`{w: 1, j: undefined}` - report success only when db has acknowledged and without writing it to the journal, because there is not entry in the journal, you could loose data.
`{w: 1, j: true}` - report success only when db has acknowledged and data has been written in the journal.
`{w: 1, j : true, wtimeout: 5000}` - report success only when db has acknowledged, data has been written in the journal and timout is less than 5s.

Here is how to specify these options:

```js
db.hobbies.insertMany(
  [
    { _id: "yoga", name: "Yoga" },
    { _id: "cooking", name: "Cooking" },
    { _id: "hiking", name: "Hiking" },
  ],
  { ordered: false, writeConcern: { w: 1, j: true, wtimeout: 1 } }
);
```

## Atomicity

MongoDB CRUD operations are atomic at the document level.

## Importing data

```bash
mongoimport -d dbname -c collection --jsonArray --drop
```

* `--jsonArray -` option tells mongoimport there are multiple docs in the file. By default it looks for one.
* `--drop` - drop the collection it if already exists

## findOne() and find()

`findOne()`` works same as `find()`

find(<query>, <projection>)

* `db.posts.find({"author.name": "Emily Watson"})` - where author is an object with name property
* `db.posts.find({tags: "coding"})` - where tags is an array of strings
* `db.posts.find({tags: {$in: ["programming", "money"]}})` - where tags is an array of string, find all the docs where tags contains "programming" and "money"
* `db.posts.find({tags: {$nin: ["programming", "money"]}})` - above line complement
* `db.posts.find({comments: {$gt: 0}})` - find all docs where comments are greater than 0
* `db.posts.find({ $and: [{ comments: { $gt: 0 } }, { comments: { $lt: 5 } }] })` - find all docs where comment is greater than 0 but less than 5

* `db.posts.find({shared: {$exists: false}})` - find all the docs where the shared property doesn't exists

```js
db.getCollection("movies").find(
{genres: {$type: "array"} },
{genres: 1}
)
```

Find all docs where type of `genres` field is an array.

```js
db.getCollection("movies").find(
{summary: {$regex: /\d{4}/} },
{summary: 1}
)
```

Find all docs where type of summary contains a 4 digit number.

```js
db.getCollection("movies").find(
{genres: {$size: 2} },
{genres: 1}
)
```

Find all docs where size of `genres` array is 2

```js
db.getCollection("movies").find(
{genres: {$all: ["Drama", "Romance"]} },.
{genres: 1}
)
```

Find all docs where `genres` array consists of "Drama" and "Romance". Note that order of "Drama" and "Romance" doesn't matter here.

```js
db.users.find({
hobbies: {$elemMatch: {title: "Sports", frequency: {$gte: 3}}}
})
```

Find all docs where hobbies (an array fo objects) contains an object with `title: sports` and frequency >= 3. Note that elemMatch performs the matching in the same document (i.e. title and frequency must be present in the same document).

Note:

```js
db.getCollection("movies").find(
  { genres: "Horror", rating: 10 },
  { genres: 1 }
);
```

By default, above query ANDs ` genres: "Horror"` and `rating: 10`. However, what if we have the following query:

```js
db.getCollection("movies").find(
  { genres: "Horror", genres: "Fantasy" },
  { genres: 1 }
);
```

Our intent was to find all docs with geners horror and fantasy. But the above query only returns docs with genres fantasy. To resolve such issues `$and` operator is used:

```js
db.getCollection("movies").find({
  $and: [{ genres: "Horror", genres: "Fantasy" }, { genres: 1 }],
});
```

`find()`` method returns a cursor. We can use the forEach loop on cursor to iterate through the object. Cursor object has many other helper methods. For e.g:

`cursor.count()` total count of records
`cursor.next()` to get the next document, if any
`cursor.hasNext()` to check whether the next document exist or not

`db.posts.find().limit(5)` - get five documents only
`db.posts.find().skip(2)` - returns all docs except the first 2
`db.posts.find().sort({comments: -1})` - sort by comments in asc order, for asc use 1
`db.posts.find().skip(2).limit(5).sort({comments: -1})` - sorts all the docs in desc order, return the 5 docs skipping the first 2. Note that here sorting is applied first before `skip()`. The `limit()` is applied at last.

All Query Operators - https://www.mongodb.com/docs/manual/reference/operator/query/

## Projection

```js
db.getCollection("movies").find({}, { "network.name": 1 });
```

Here `network` is an embedded docuemnt, this would return all the docs with field `network.name` only.

## updateOne() and updateMany()

```js
updateOne(<query>, <update>, <options>)
updateMany(<query>, <update>, <options>)
```

```js
db.posts.updateOne(
{postId: 2618},
{$set: {shared: true}},
)
```

update the shared key of doc with postId 2618 to true

```js
db.posts.updateOne(
{postId: 2618},
{$unset: {shared: 1}},
)
```

remove the shared property from the doc with postId 2618

```js
db.posts.updateOne(
{postId: 8451},
{$inc: {comments: 2}},
)
```

increment the comments property by 2 of doc with postId 8451

```js
db.posts.updateOne(
{postId: 3511},
{$pull: {tags: "programming"}},
)
```

remove the string programming from the `tags` array

```js
db.posts.updateOne(
{postId: 3511},
{$push: {tags: "programming"}},
)
```

append the string programming to the `tags` array

## deleteOne() and deleteMany()

```js
deleteOne(<query>)
deleteMany(<query>)
```

## aggregation

db.posts.aggregate([
{$group: {_id: "$author.nickname"}}
])

group by nickname

## index

`db.posts.getIndexes()` to get the current indexes

`db.student.find().explain("executionStats")` - to get query stats, executionStats.totalDocsExamined returns the docs needed to examied to return the result.

`db.student.createIndex({firstName: 1})` - create a new index for `firstName` on `asc` order. If you now query on `firstName`, `executionStats.totalDocsExamined` should return `1`.

## Aggregate

```js
db.persons.aggregate([
    {$match: {gender: "female"} },
    {$group: {_id: {state: "$location.state"}, totalPerson: {$sum: 1} }},
    {$sort: {totalPerson: -1}}
]);
```

Projection:

```js
db.persons.aggregate([
    {$project: {_id: 0, gender: 1, fullName: {$concat: [ { $toUpper: "$name.first" }," ", "$name.last"]} }}
])
```