# Data Models and Query Languages

Data models are the most important part of developing software, because they have such a profound effect, not only on how the software is written, but also on how we think about the problem that we are solving.

Most application are built by layering one data model on top of another, for each layer the key question is it represented in therm of the next lower layer?

Example:
1. we look at real world in which there are people orgs, goods, action, money, flows, sensors etc, and model in terms of objects or data structures and API that manipulate those data strcutures those structures are often specific to your application.
2. When you want to store those data structures you express them in terms of a general purpose data model, such as JSON or XML documents tables in a relational database or graph model.
3. The engineers who built your database software decided on a way of representing that JSON/XML/relational/graph data in terms of bytes in memory on disk or on a network. The representation may allow the data to be queried searched, manipulated and processed in various ways.
4. On lower levels hardware engineers have figured out how to represent bytes in terms if electrical current, pluses of light, magnetic fields and more.
5. Each layer hides the complexity of the layers below it by providing a clean data model.

## [[Relational Model]] versus [[Document Model]]

The best known data model today is probably that of SQL, based on the relational model proposed by Edgar Codd in 1970. Data is organised into relations (called tables in SQL) where each relation is an unorderd collection of tuples (rows in SQL) 

RDBMS and SQL is the tool to store and query data with some kind of reqular structure.

## The Birth of NoSQL

Driving force behind the adoption of NoSQL database, including:

- A need for greater scalability than relational databases can easily achieve, including very large datasets or very high write throughput.
- A wider spread preference for free and open source software over commercial database products.
- Specialised query operations that are not well supported by the relational model.
- Frustration with the restrictivbeness of relational schemas, and a deasire for a more dynamic and expressive data model.

>[!Choice of technology depends on the requirements of application.]
>RDBMS will continue to be sued along side a broad variety of non relational datastores an idea that is something called [[Ployglot]] persistence.

## The Object-Relational Mismatch

App development today is mostly done in OOP, which leads to a criticism of the SQL data model, if data is stored in relational tables and awkward translation layer is required between the object in the application code and the databases model of tables, rows and columns . This disconnect between models is called impedance mismatch.

ORMs reduce the boilerplate code required for the translation layer but they cant completely hide the differences between the two models.

Example:
1. Think about linkedin profile, generally data are normalized and sotedf in seperate table with foreign key as one to one or one to many relation, like work exp, education, certification etc.  DB's have support for storing one to many data in a JSON or XML but it becomes very difficult to query the fields inside JSON. 
2. Instead of using a RDBMS to store the linkedin profile, we can use Document oriented databases like MongoDB, RethinkDB, CouchDB and Expresso.
3. JSON representation has better locality than the multi table schema.
3. In RDBMS if you want to fetch a profile you will have to do JOIN between multiple tables to find the data.
In JSON representation, all the relevant information is in one place and one query is sufficient.

## Many to one and Many to Many Relationships

There are advantage of having stabndardized lists of geographic regions and industries and letting  choose from a dropdown as autocomplete.

- Consistent style and spelling across profiles
- Avoiding ambiguity
- Ease  of update
- Localization support
- Better Search

When you store Id, it reduces the duplication of data. ID is relevant to the database. If you store data as text directly you are duplicating the human meaningful information in every record that uses it.

Id has no meaning to humans it never needs to change the ID remain same, even if the information in identifies changes.

Removing such duplication is the key idea behind normalisation in databases.
n relational atabaes, it's normal to refer to rows in other tables ID, because joins are easy.
In Document DB, joins are not needed for one to many, tree structures and support for joins are weak.

If the DB it self does not support joins you have to emulate a join in application code by making multiple queries to DB.

Data has a tendency of becoming more inter connected as features are added to application, even if we are using Doc type DB.

Example: Company, School, college etc have their own profile so now instead of name they would use the id of those in the resume and by join we will fecth the related data.


## Are Document Databases Repeating History?

IMS designed by IBM used a fairly simple data model called the hierarchical mdel. which has some similarities to the JSON model, used by document database. It represented all data as tree of records nested within records, much like JSON.

IT worked well for one to many relations but made many to many relationships difficult, it didn't support JOIN, Developers had to decide whether to duplicate data or to manually resolve references from one record to another.

relational model (which became SQL) and the network model were proposed as solution for above mentioned problem of the Document DB.

### The Network Model

The CODASYL model was a generalisation of the hierarchical model, in the tree structure of hierarchical model, every record has exactly one parent in the network models a record could have multiple parents.

The links between records were not foreign key but more like pointers in a programming language, The only way of accessing a record was to follow a path from a root record along these chains of links. This was called an access path.

If a record has multiple parents, the application code has to keep tracks of all the various relationships. it felt like navigating around an N- dimensional data space.

### The Relational Model

What the relational model did by contrast. as to lay out all the data in the open, a relational (table) is simply a collection of tuples (rows) no nested structures, no complicated access path.

You can read any or all of the rows in a table, selecting those that match an arbitrary condition.

The query optimize automatically decides which part of the query to executer in which orders and which indexes to use. Those choices are effectively the "access path", but the big difference is that they are made automatically by the query optimiser, not by the application developer.

## Comparison to document databases

Document database reverted back to the hierarchical model in one aspect: storing nested records withion their parent's record rather than in a separate table.

When it comes to representing many to many relationship, both are not fundamentally different in both cases, the related item is referenced by a unique identifier called Foreign Key in Relational DB and document reference in document model. That identifier is resolved at read time by using join or follow up queries.


### Relational versus Document DB Today

Doc DB:
- Schema flexiblity
- better performance due to locality
- close to the data structures used by application

Relational DB:
- better support for join
- Many to one and Many to many relationships


### Which data model leads to simpler applications code?

**Doc Model**: if application has document like structure ( a tree of one to many relationship, where typically the entire tree is loaded at once) then use Doc Model.

**RD**: The relational technique of shredding splitting a documentlike structure into multiple tables can lead to cumbersome schemas and unnecessarily complicated application code.

**Doc Model**: you can not refer directly to a nested item within a document but you need to something like "the second item in ther list of positions for user 215, as lonmg as document are not too depply nested that is not usually a problem"

**Doc Model**: Poor support for joins in document DB. May or may not be problem depending on the application.

For many to many relationship doc model DB,a re not that great, It's possible to reduce the need for joins by denormalising, but then the app code needs to do extra work to keep the denormalised data consistent.

Joins can be emulated in application code by making multiple requests to the database, but that also moves complexity into the application and is usially slower than a join performed by specialized code inside the database.

>[! Note: ]
>we cant say which data model leads to simpler app code, it depends on the kinds of relationships that exists between data items. For highly interconnected data, the document model is awkward the relational model is acceptable and graph models are the natural.

### Schema flexibility in the document model.

Document DB and the JSON support in relational DB, do not enfoce any schema on the data in document. XML support in DB comes with optional schema validation

No schema means that arbitrary keys and values can be added to a document, and when reading clients have no guarantees as to what fields the documents may contain.

Document DB are called schema less, its misleading, as the code that reads the data usually assumes some kind of structure, that is, there is an implicit schema, its not forced by DB. Doc DB's are schema on read with contrast with schema on write on RD.

Its like dynamic type checking (Doc DB) and compile time type checking (RD)

Schema on read are useful when:
- There are many different types of objects and it is not practical to put each of objects in its own table.
- The strctuire of the data is determined by external systems over which you have no contrast and which may change at any time.

## Data Locality for Queries`

A document is usually stored as a single continous string, encoded as JSON, XML, or a binary variant thereof (MOngo DB's BSON).

If you need to show the entire data on web page it gives you advantage at data is at one palce. index lookup which may require more disk seep and take more time.

The locality advantage only applies when:
- You need large parts of the document at the same time.
- DB's generally loads entire doc, even if you access only a small portion of it, which would be wasteful on large document.
- On update to a document, the entire document usually needs to be re written only modifications that don't change the encoded size of a document can easily be performed in place.
- Its recommended that you keep documents failry small and avoid writers that increase the size of a document.
- Grouping relaterd ata together for locality is not limited to the document model. Google's spanner DB, offers the same locality schema to declare that a table's rows should be interleaved (nested) within a table. Oracle allows the same using a feature called multi table index cluster tables. The column family concept in the big table data model (Cassandra and H Base) has a similar purpose of managing locality.

## Convergence of document and relational databases.

Most RDBMS supports XML and have functions to make local modifications to XML documents and ability to index and query inside XML document which allows applications to use data models very similar to what they would do when using a document database.

Many popular RDBMS supports JSON documents, and many Doc type DB are supporting JOINS, like relational models on client side, although this is likely to be slower than a joined performed in the database since in requires additional network round trip.

Doc and Relational databases are becoming more similar over time, the data models complement each other. If a database is able to handle document -like data and also perform relational queries on it, applications can use the combination of features that best fits their needs.


