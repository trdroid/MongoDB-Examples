### MongoDB shell

MongoDB shell allows interacting and administering the MongoDB database server.

It is a JavaScript shell that allows interaction with the server through a JavaScript API .

<b>Run MongoDB shell</b>

Ensure that the <i>mongod</i> instance is running before running the MongoDB shell.

> $ mongo

A database name can also be provided at startup.

### CREATE

<b>Creating a database</b>

Database and collections are created only when the documents are first inserted. This means you can switch to use a database or add to a collection that does not yet exist. It is for the same reason that a database name that does not yet exist can be provided when starting up the shell.

Since the namespace is dynamically allocated, accidental creation of databases or collections can be prevented by enabling the "strict" mode.

<b>Switch to a database "sample"</b>

    > use sample
    switched to db sample
    > 

db refers to the "sample" database after switching to it.

<b>Inserting a document</b>

Insert a document into a collection called "users". Again, "users" does not exist before the statement is executed. It is dynamically created upon inserting the first document.

```javascript
    > db.users.insert({username: 'Bob'})
    WriteResult({ "nInserted" : 1 })
```


The document inserted is "{username: 'Bob'}". Documents are represented in JSON format.

On executing the statement, a small pause implies the creation of the database "sample" and the collection "users".

<b>Insert more documents</b>

```javascript
    > db.users.insert({username: 'Keith'})
    WriteResult({ "nInserted" : 1 })
    > db.users.insert({username: 'David'})
    WriteResult({ "nInserted" : 1 })
```

<b>Get a count of all documents in the collection</b>

Use the count() method on the collection

```javascript
> db.users.count()

    3
```


### READ

<b>Query the database collection: Get all documents</b>

Use find() method to return all documents in a collection.


```javascript
    > db.users.find()
    { "_id" : ObjectId("56b95154b33e3f9978550173"), "username" : "Bob" }
```


Every MongoDB document requires an _id field, which acts as the document's primary key. It is unique among all _id values in the collection.

Upon inserting a document in a collection that does not have an _id field, a MongoDB object ID is generated and added to the document.

<b>Finding specific documents</b>

The find() method can be passed a query selector, which is a document that's used to match against all documents in the collection and returns all the matching documents. 

```javascript
> db.users.find({username: 'David'})

    { "_id" : ObjectId("56b95a26b33e3f9978550175"), "username" : "David" }
```

Attempting to find a document that does not exist returns nothing

```javascript
> db.users.find({username: 'Tom'})
```

### UPDATING

Updates can be documents by using the update() method on a collection.

The update() method requires two arguments:
* a query selector to specify which document(s) to update, specified in JSON format
* how to update the document and what to update it with, specified in JSON format

<i>Add a new attribute (age) for an user</i>

A new property can be added to an existing document by using the update() method on the collection and passing it 
* the query selector of the documents to be updated
* using the $set operator to set a new property which is passed in as a JSON

```javascript
> db.users.update({username: 'Keith'}, {$set: {age: 25}})

    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

The update() method finds the documents with the username 'Keith' and sets the 'age' property to 25

When queried for the document containing 'Keith', the newly added property can be seen.

```javascript
> db.users.find({username: 'Keith'})

    { "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "age" : 25 }
```

<i> Remove an existing attribute </i>

To remove an existing property, use the update() method on an the collection and passing it
* the query selector of the documents to be updated
* using the $unset operator to remove the property, which is specified in JSON format

```javascript
> db.users.update({username: 'Keith'}, {$unset: {age: 0}})

    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

```javascript
> db.users.find({username: 'Keith'})

    { "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith" }
```

<i> Adding an object as a property </i>

A new property "drafts" is added by using the $set operator. Unlike the "age" property added previously which points to a single value, the "drafts" property points to an object with key-value pairs.

```javascript
db.users.update( {username: 'Keith'}, {$set: { drafts: {
		documents: ["doc1", "doc2"],
		emails: ["email15", "email18"]
	}
}})
```
Pasting the above in the command line:

```javascript
> db.users.update( {username: 'Keith'}, {$set: { drafts: {
... documents: ["doc1", "doc2"],
... emails: ["email15", "email18"]
... }
... }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

Querying:

```javascript
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2" ], "emails" : [ "email15", "email18" ] } }
```
    
<b> Updating a nested object </b>

MongoDB's query language has the ability to reach and update nested objects.

To update the new set of "documents" which are "drafts":

```javascript
db.users.update( {username: 'Keith'}, {$set: { drafts: {
		documents: ["doc1", "doc10"]	
	}
}})
```

Running it in shell:

```javascript
> db.users.update( {username: 'Keith'}, {$set: { drafts: {
... documents: ["doc1", "doc10"]
... }
... }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
```

Querying:

```javascript
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc10" ] } }
```
