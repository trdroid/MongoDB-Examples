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

By default, the update() method performs an update operation only on the first matching document. 

To apply the update operations to all matching documents, then a flag must be specified in the update() method.

The multi-update flag is indicated by the fourth argument to the update() method.

```javascript
> db.users.update({username: 'Keith'}, 
					{$set: {age: 25}},
					false,
					true)  //<------ multi-update flag
```

When queried for the document containing 'Keith', the newly added property can be found in the document.

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
    
<b> Overwrite a property's value </b>

To overwrite the "drafts" to only "documents": doc1 and doc10, set the property again

```javascript
db.users.update( {username: 'Keith'}, {$set: { drafts: {
		documents: ["doc1", "doc10"]	
	}
}})
```

This overwrites the value of "drafts" object to contain only "documents": [doc1, doc10]

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

Reassign "drafts" with "documents" and "emails" to 'Keith'.

<b> Querying a nested object using a dot notation </b>

To find all users containing "drafts" of "documents" with id: doc10

```javascript
> db.users.find({'drafts.documents' : 'doc1'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2" ], "emails" : [ "email15", "email18" ] } }
```
Notice how the key of the query selector uses the dot notation to refer to a nested object "documents" (nested inside "drafts"). 

The dot notation instructs the query engine to look for a key named 'drafts' that points to an object with an inner key "documents" that contains the value "doc1"

To find all users containing "drafts" of "emails" with id: "email15"

```javascript
> db.users.find({'drafts.emails' : 'email15'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2" ], "emails" : [ "email15", "email18" ] } }
```

<b> Updating a nested object </b>

To update a nested object, the first approach is to use a $set operator, which requires the entire object to be read, updated and written back replacing the previous value. 

A better approach is to rely on MongoDB's query language as it has the ability to reach and update nested objects.

To just add an element to the list, use either of the following operators
* $push - duplication allowed
* $addToSet - prevents duplication
 
An attempt to update the "documents" for 'Keith'
* Find a document that matches username 'Keith'
* update the found document by adding to 'documents' in 'drafts' 

Here are some failed attempts:

```javascript
db.users.update({username: 'Keith'}, { $push : { drafts : {
		documents: ["doc2", "doc5"]
	}
}})
```

```javascript
> db.users.update({username: 'Keith'}, { $push : { drafts : {
... documents: ["doc2", "doc5"]
... }
... }})
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 0,
	"nModified" : 0,
	"writeError" : {
		"code" : 16837,
		"errmsg" : "The field 'drafts' must be an array but is of type Object in document {_id: ObjectId('56b95a19b33e3f9978550174')}"
	}
})

> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2" ], "emails" : [ "email15", "email18" ] } }

> db.users.update({username: 'Keith'}, { $push : { drafts : {
... documents: "doc2"
... }
... }})
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 0,
	"nModified" : 0,
	"writeError" : {
		"code" : 16837,
		"errmsg" : "The field 'drafts' must be an array but is of type Object in document {_id: ObjectId('56b95a19b33e3f9978550174')}"
	}
})

> db.users.update({username: 'Keith'}, { $push : { drafts.documents : {
... ["doc2", "doc5"]
... }
... }})
2016-02-09T11:26:24.220-0500 E QUERY    [thread1] SyntaxError: missing : after property id @(shell):1:55

//SUCCESSFUL
> db.users.update({username: 'Keith'}, { $push : { 'drafts.documents' : "doc5" }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

//doc5 added to drafts.documents
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5" ], "emails" : [ "email15", "email18" ] } }
```
