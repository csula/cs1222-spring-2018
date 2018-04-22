# Introduction to NoSQL Database



## Agenda

* What is NoSQL
* Why NoSQL
* Install MongoDB
* Import sample data
* CRUD with Mongo



## What

* "next generation" type of database
* non-relational
* distributed
* open source 
* horizontal scalable



## Why

* To help with rapidly changing data types



## Install MongoDB

https://github.com/csula/Utilities/blob/master/setups/mongo.md



## Import sample data


* Download https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json


### Command


#### MacOS

```sh
mongoimport --db test
    --collection restaurants --drop
    --file ~/downloads/primer-dataset.json
```

#### Windows

```sh
"C:\Program Files\MongoDB\Server\3.6\bin\mongoimport.exe" --db test
    --collection restaurants --drop
    --file C:\Users\IEUser\Downloads\primer-dataset.json
```



## Mongo shell commands

* `mongo`
* For windows user, look for `mongo.exe` under your MongoDB folder



## CRUD with Mongo shell


```sh
use test;
```


### Common debugging commands


```sh
# to see all databases
# equals to `show databases;` in MySQL
show dbs; 

# to see all collections
# equals to `show tables;` in MySQL
show collections;
```


### Insert


```javascript
db.restaurants.insert(
   {
      "address" : {
         "street" : "2 Avenue",
         "zipcode" : "10075",
         "building" : "1480",
         "coord" : [ -73.9557413, 40.7720266 ]
      },
      "borough" : "Manhattan",
      "cuisine" : "Italian",
      "grades" : [
         {
            "date" : ISODate("2014-10-01T00:00:00Z"),
            "grade" : "A",
            "score" : 11
         },
         {
            "date" : ISODate("2014-01-16T00:00:00Z"),
            "grade" : "B",
            "score" : 17
         }
      ],
      "name" : "Vella",
      "restaurant_id" : "41704620"
   }
);
```


### Read


```javascript
// to find all documents under `restaurants`
db.restaurants.find()

// to find a specific restaurants with a column
db.restaurants.find( { "borough": "Manhattan" } )

// or you can even find a field in an embed document 
db.restaurants.find( { "address.zipcode": "10075" } )

// query against a field of an array
db.restaurants.find( { "grades.grade": "B" } )
```


```javascript
// if you want to use grater sign
db.restaurants.find( { "grades.score": { $gt: 30 } } )

// or less sign
db.restaurants.find( { "grades.score": { $lt: 10 } } )

// what about logical operation like and and or?
// AND
db.restaurants.find( { "cuisine": "Italian", "address.zipcode": "10075" } )

// OR
db.restaurants.find(
   { $or: [ { "cuisine": "Italian" }, { "address.zipcode": "10075" } ] }
)
```


```javascript
// sorting
db.restaurants.find().sort( { "borough": 1, "address.zipcode": 1 } )

// when you call find command you actually do not retrieve any object back
// to retrieve objects back you will need to call `toArray()`
db.restaurants.find().toArray()

// projection
// projection allows you to select certain attributes when retrieve JSON
db.restaurants.find({}, {borough: 1});
```


### To update a document

```javascript
// update takes a query object first and then the fields to update
db.restaurants.update(
    { "name" : "Juni" },
    {
      $set: { "cuisine": "American (New)" },
      $currentDate: { "lastModified": true }
    }
)
```


```javascript
// can also update embed document
db.restaurants.update(
  { "restaurant_id" : "41156888" },
  { $set: { "address.street": "East 31st Street" } }
)

// update multiple documents at once
db.restaurants.update(
  { "address.zipcode": "10016", cuisine: "Other" },
  {
    $set: { cuisine: "Category To Be Determined" },
    $currentDate: { "lastModified": true }
  },
  { multi: true}
)
```


```javascript
// remember if you don't specify $set then it will replace the whole document
db.restaurants.update(
   { "restaurant_id" : "41704620" },
   {
     "name" : "Vella 2",
     "address" : {
              "coord" : [ -73.9557413, 40.7720266 ],
              "building" : "1480",
              "street" : "2 Avenue",
              "zipcode" : "10075"
     }
   }
)
```


### Delete documents


```javascript
// remove all documents meeting this condition
db.restaurants.remove( { "borough": "Manhattan" } )

// to be on the safe side you can use `justOne` to remove a document at once
db.restaurants.remove( { "borough": "Queens" }, { justOne: true } )

// if you want to remove the entire documents
db.restaurants.remove( { } )

// or you can drop the whole document
db.restaurants.drop()
```



## SQL to MongoDB

| SQL Terms/Concepts | MongoDB Terms/Concepts |
| :-- | :-- |
| database | database |
| table | collection |
| row | document |
| column | field |
| index | index |
| table joins | embedded documents and linking |


| SQL statements | MongoDB statements |
| :-- | :-- |
| `CREATE TABLE users ( ... )` | `db.users.insert({ ... })` |
| `ALTER TABLE users ADD column DATETIME` | `db.users.update({}, { $set: { column: new Date() } }, { multi: true })` |



## Reference

API documentation: https://docs.mongodb.com/manual/crud/
