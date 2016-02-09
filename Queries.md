### MongoDB shell

MongoDB shell allows interacting and administering the MongoDB database server.

It is a JavaScript shell that allows interaction with the server through a JavaScript API .

<b>Run MongoDB shell</b>

Ensure that the <i>mongod</i> instance is running before running the MongoDB shell.

> $ mongo

A database name can also be provided at startup.

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

    > db.users.insert({username: 'Bob'})
    WriteResult({ "nInserted" : 1 })

The document inserted is "{username: 'Bob'}". Documents are represented in JSON format.

On executing the statement, a small pause implies the creation of the database "sample" and the collection "users".

Query the database ("sample") collection ("users")

    > db.users.find()
    { "_id" : ObjectId("56b95154b33e3f9978550173"), "username" : "Bob" }

Every MongoDB document requires an _id field, which acts as the document's primary key. It is unique among all _id values in the collection.

Upon inserting a document in a collection that does not have an _id field, a MongoDB object ID is generated and added to the document.



