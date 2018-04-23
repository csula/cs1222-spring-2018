# MongoDB Aggregation



## Agenda

* What are aggregations?
* Recap on SQL `Group By`
* Recap on Mongo read
* Aggregation pipeline



## What are aggregations?

* process data records and return computed results.



## Recap on SQL Group By


```sql
SELECT ArtistID, COUNT(*) FROM Artists
INNER JOIN Titles
ON Artists.ArtistID = Titles.ArtistID
GROUP BY ArtistID;
```


## Recap on Mongo Read


```js
db.collection.find();

// use pretty to print pretty
db.colleciton.find().pretty();

// simplest format of aggregation
// use count to count number
db.collection.find().count();

// or use dinstinct to get unique set of results
db.collection.find().distinct("fieldName");
```


### projection


```js
db.collection.find( <query filter>, <projection> )
```



## Aggregation pipeline

![aggregation pipeline](https://raw.githubusercontent.com/csula/cs1222-spring-2018/master/notes/imgs/aggregation-pipeline.png)


### Learn by example

Download http://media.mongodb.org/zips.json


Each document in the `zips` collection has the following form:

```json
{
	"_id": "10280",
	"city": "NEW YORK",
	"state": "NY",
	"pop": 5574,
	"loc": [
		-74.016323,
		40.710537
	]
}
```


### Return states with Populations above 10 Million


```js
db.zips.aggregate( [
	{ $group: { _id: "$state", totalPop: { $sum: "$pop" } } },
	{ $match: { totalPop: { $gte: 10 * 1000 * 1000 } } }
] )
```


### Aggregate functions


| Name | Description |
|:--|:--|
| [$sum](https://docs.mongodb.com/manual/reference/operator/aggregation/sum/) | return a sum of numerical values. Ignore non-numeric values. |
| [$avg](https://docs.mongodb.com/manual/reference/operator/aggregation/avg/) | returns an average of numerical values. Ignore non-numeric values. |
| [$first](https://docs.mongodb.com/manual/reference/operator/aggregation/first/) | returns a value from the first document for each group. Order is only defined if the documents are in a defined order. |


| Name | Description |
|:--|:--|
| [$last](https://docs.mongodb.com/manual/reference/operator/aggregation/last/) | similar to above but returns last document. |
| [$max](https://docs.mongodb.com/manual/reference/operator/aggregation/max/) | returns the highest expression value for each group. |
| [$min](https://docs.mongodb.com/manual/reference/operator/aggregation/min/<Paste>) | similar to above but returns the lowest |


| Name | Description |
|:--|:--|
| [$push](https://docs.mongodb.com/manual/reference/operator/aggregation/push/) | return an array of expression values for each group. |
| [$addToSet](https://docs.mongodb.com/manual/reference/operator/aggregation/addToSet/) | returns an array of unique expression values for each group |
| [$stdDevPop](https://docs.mongodb.com/manual/reference/operator/aggregation/stdDevPop/) | returns the population standard deviation of the input values. |
| [$stdDevSamp](https://docs.mongodb.com/manual/reference/operator/aggregation/stdDevSamp/) | returns the sample standard deviation of the input values. |


### Return average city population by state

```sql
SELECT state, SUM(pop) AS totalPop
FROM zips
GROUP BY state
HAVING totalPop >= (10 * 1000 * 1000); 
```


```js
db.zips.aggregate( [
	{ $group: { _id: { state: "$state", city: "$city" }, pop: { $sum: "$pop" } } },
	{ $group: { _id: "$_id.state", avgCityPop: { $avg: "$pop" } } }
] )
```


```json
{
	"_id": {
		"state": "CO",
		"city": "EDGEWATER"
	},
	"pop": 13154
}
```


### Return largest and smallest cities by state

```js
db.zips.aggregate( [
   { $group:
      {
        _id: { state: "$state", city: "$city" },
        pop: { $sum: "$pop" }
      }
   },
   { $sort: { pop: 1 } },
   { $group:
      {
        _id : "$_id.state",
        biggestCity:  { $last: "$_id.city" },
        biggestPop:   { $last: "$pop" },
        smallestCity: { $first: "$_id.city" },
        smallestPop:  { $first: "$pop" }
      }
   },

  // the following $project is optional, and
  // modifies the output format.

  { $project:
    { _id: 0,
      state: "$_id",
      biggestCity:  { name: "$biggestCity",  pop: "$biggestPop" },
      smallestCity: { name: "$smallestCity", pop: "$smallestPop" }
    }
  }
] )
```



## References:

* [SQL aggregation to MongoDB aggregation comparison](https://docs.mongodb.com/manual/reference/sql-aggregation-comparison/)
* [Aggregation pipeline API documentation](https://docs.mongodb.com/manual/reference/operator/aggregation/)
