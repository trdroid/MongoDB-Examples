### MongoDB Server Version

```shell
$ mongod --version
db version v3.2.1
git version: a14d55980c2cdc565d4704a7e3ad37e4e535c1b2
OpenSSL version: OpenSSL 1.0.1f 6 Jan 2014
allocator: tcmalloc
modules: none
build environment:
    distmod: ubuntu1404
    distarch: x86_64
    target_arch: x86_64

```

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

<b> ALSO NOTICE: when using a dot operator, the expression that contains the dot operator should be enclosed in quotes </b>

	In db.users.find({username: 'Keith'}), username is not enclosed in quotes, whereas 
	
	In db.users.find({'drafts.documents' : 'doc1'}), drafts.documents which uses a dot operator is enclosed in quotes
	
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

Here are the queries from above that failed:

```javascript
/*
 didn't work because the $push operator expects a JSON object with a key that points to an array
 that is why the error says "The field 'drafts' must be an array but is of type Object in document",
 as $push expects drafts in the found document to be array but it turned out to be an Object in the document
*/	
db.users.update({username: 'Keith'}, { $push : { drafts : {
		documents: ["doc2", "doc5"]
	}
}})

//failed for the same reason, $push expects drafts to be an array
db.users.update({username: 'Keith'}, { $push : { drafts : {
		documents: "doc2"
	}
}})

/*
 even though drafts.documents is an array, it has to be enclosed in quotation marks!! 
 Remember! an expression containing a dot operator should be enclosed in quotes, else it results in a syntax error
 this is the reason for the syntax error
*/
db.users.update({username: 'Keith'}, { $push : { drafts.documents : ["doc2", "doc5"]
}})
```

Here is the query that was successful:

```javascript
/*
 works because $push is passed a json object with the key enclosed in quotes and which points to an array object
 The new value "doc5" is pushed into the array pointed to by 'drafts.documents'
*/ 
db.users.update({username: 'Keith'}, { $push : { 'drafts.documents' : "doc5" }})
```

Using the correct syntax for pushing, lets observe the following:

```javascript
//attempting to push an array object into an array
> db.users.update({username: 'Keith'}, { $push : { 'drafts.documents' : ["doc5", "doc7"]
... }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

//the 'drafts.documents' now contains an array entry, so to add multiple documents, add one after another, not as an array
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5", [ "doc5", "doc7" ] ], "emails" : [ "email15", "email18" ] } }

//reset to original values
> db.users.update( {username: 'Keith'}, {$set: { drafts: {
... documents: ["doc1", "doc2", "doc5"],
... emails: ["email15", "email18"]
... }
... }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

//query to verify
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5" ], "emails" : [ "email15", "email18" ] } }
```

Adding a duplicate value using $push

```javascript
//adding a duplicate document 'doc5' which is allowed with the $push operator
> db.users.update({username: 'Keith'}, { $push : { 'drafts.documents' : "doc5" }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

//notice the duplicate value
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5", "doc5" ], "emails" : [ "email15", "email18" ] } }
```

Avoid entering duplicate values using $addToSet operator

```javascript
/*
attempting to add a duplicate value 'doc2' using the $addToSet operator. 
The following executes but does not add the duplicate value
*/
> db.users.update({username: 'Keith'}, { $addToSet : { 'drafts.documents' : "doc2" }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 0 })

//Notice there is only a single 'doc2'
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5", "doc5" ], "emails" : [ "email15", "email18" ] } }
```

<i>Using a dot operator in the update method's query selector's key</i>

Let's assume that every drafted email with id 'email15' is linked to a drafted document with id 'doc17', add 'doc17' to 'documents' for every user with 'email15' in 'emails'

```javascript
/*
 query selector uses a dot operator in the key
 
 if drafts.emails for any user contains emails15, the add doc17 to the users documents
*/
> db.users.update({'drafts.emails' : 'email15'}, { $addToSet : { 'drafts.documents' : "doc17" }})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

//verify 
> db.users.find({username: 'Keith'})
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5", "doc5", "doc17" ], "emails" : [ "email15", "email18" ] } }
```

### DELETING

The remove() method can be used to remove documents. 

To remove specific documents, pass it a query selector.

```javascript
> db.users.find()
{ "_id" : ObjectId("56b95154b33e3f9978550173"), "username" : "Bob" }
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5", "doc5", "doc17" ], "emails" : [ "email15", "email18" ] } }
{ "_id" : ObjectId("56b95a26b33e3f9978550175"), "username" : "David" }

//Remove a document with 'username': 'Bob'
> db.users.remove({username: 'Bob'})
WriteResult({ "nRemoved" : 1 })

//Verify
> db.users.find()
{ "_id" : ObjectId("56b95a19b33e3f9978550174"), "username" : "Keith", "drafts" : { "documents" : [ "doc1", "doc2", "doc5", "doc5", "doc17" ], "emails" : [ "email15", "email18" ] } }
{ "_id" : ObjectId("56b95a26b33e3f9978550175"), "username" : "David" }
```

<i>Using a dot operator in the query selector's key in the remove() method </i>

```javascript
/*
 remove the documents which contains 'email15' in 'drafts.emails'
*/
> db.users.remove({'drafts.emails': 'email15'})
WriteResult({ "nRemoved" : 1 })

//verify
> db.users.find()
{ "_id" : ObjectId("56b95a26b33e3f9978550175"), "username" : "David" }
```

Adding more records

```javascript
> db.users.insert({username: 'Tom'})
WriteResult({ "nInserted" : 1 })
> 
> db.users.insert({username: 'Jerry'})
WriteResult({ "nInserted" : 1 })
> 
> db.users.insert({username: 'Donald'})
WriteResult({ "nInserted" : 1 })

> db.users.find()
{ "_id" : ObjectId("56b95a26b33e3f9978550175"), "username" : "David" }
{ "_id" : ObjectId("56ba2556b33e3f9978550176"), "username" : "Tom" }
{ "_id" : ObjectId("56ba2556b33e3f9978550177"), "username" : "Jerry" }
{ "_id" : ObjectId("56ba2556b33e3f9978550178"), "username" : "Donald" }
```

Attempting to call remove() method on a collection to clear ONLY its documents

```javascript
> db.users.remove()
2016-02-09T12:45:19.044-0500 E QUERY    [thread1] Error: remove needs a query :
DBCollection.prototype._parseRemove@src/mongo/shell/collection.js:333:1
DBCollection.prototype.remove@src/mongo/shell/collection.js:356:18
@(shell):1:1
```

Calling the drop() method on a collection, removes all of its documents and indexes.

```javascript
> db.users.drop()
true
> db.users.find()
> 
```


