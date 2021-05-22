
# MongoDB Tutorial

-------------------------------------

## What is MongoDB ?
--------------------------------


* It is document-oriented NoSQL database 
* It is **Schema-free** (based on binary JSON known as **BSON**)
* It is organized in group of documents called **collections**
* It uses simple query language. Rich, document-based queries
* It is open source

## MongoDB vs RDBMS

-------------------------------------

* **Collection** vs table
* **Document** vs row
* **Field** vs column
* Collection is not strict about what goes into it(it is schema-less)


##### DATA TYPES IN MONGODB
---------------------------------

Data Type | Example | Number | Alias
--------- | -----------|------|--------
string | "How are you?"| 2 | "string"
32-bit integer | 393 | 16 | "int"
Double | 646.44 | 1 | "double"
Boolean | true, false | 8 | "bool"
Array | [1, 3, 5] | 4 | "array"
Object | {attr1: "val1", attr2 : "val2"} | 3 | "object"
Date | new Date(\<yyyy-mm-dd>) | 9 | "date"
objectId | \<ObjectId> | 7 | "objectId"
Null | null | 10 | "null"
Regular Expression |  | 11 | "regex"
JavaScript |  | 13 | "javascript" 
Timestamp | | 17 | "timestamp"
Binary Data | | 5 | "binData" 
64-bit integer | | 18 | "long" 




#### CRUD OPERATIONS 
-------------------------


##### 1. CREATE OPERATION 
---------------------------------------

###### Create Database 
```
    use databaseName
```

###### Create Collection 
```
    db.createCollection("collectionName")
```

###### Create Document

> Create & insert One Document 
This will automatically insert _id attribute to the document 
```
    db.collectionName.insertOne({attr1: 'val1', attr2: 'val2',
    ... })
```

For custom _id attribute 
```
    db.collectionName.insertOne({_id_: 'val1', attr2: 'val2',
    ... })
```

> Create & insert multiple documents 
```
    db.collectionName.insertMany([
        {
            attr1: 'val1',
            attr2: 'val2'
        },
        {
            attr3: 'val3',
            attr1: 'val4',
            attr4: 'val5' 
        }
    ])
```



##### 2. READ(RETRIEVE) OPERATION 
---------------------------------------

###### Read All Documents 
```
    db.collectionName.find({})
```

###### Turn off the fields we do not want

Do not include _id in the resultant set
of documents 
```
    db.collectionName.find({}, {_id: 0})
```

###### Limit the number of documents returned 
```
    db.collectionName.find({}).limit(numberofDocuments)
```

###### Sort the documents returned
* 1: For Ascending 
* -1: For Descending

Here sort the returned set of documents **first by fieldName1 then by fieldName2**
```
    db.collectionName.find({}).sort(fieldName1: 1, fieldName2: 1)
```

**AND Logic**
> Find all students who are chemistry major and named Jack 
```
    db.students.find({major: "Chemistry", name: "Jack"})
```

**OR Logic**
> Find all students who are chemistry major and named Jack 
```
    db.students.find({ $or: [{major: "Chemistry"}, {name: "Jack"}]})
```

> Find all students who have gpa >= 4
```
    db.students.find({gpa: {$gte: 4}})
```



###### Comparison strings in MongoDB

* $eq  - Equal
* $ne  - Not Equal
* $lt  - Less than
* $lte - Less than Equal
* $gt  - Greater than
* $gte - Greater than Equal



**in**
> Find all students whose name is in ["santosh", "subhash", "sangeeta"]
```
    db.students.find({$in: ["santosh", "subhash", "sangeeta"]})
```


**exists**
> Find all students which contain a field 'awards' 

```
    db.students.find({awards: {$exists: true}})
```

**type**
> Find all students which contain a field 'name' of type string 

2 corresponds to string

```
    db.students.find({name: {$type: 2}}) 
```

**Specific item of an array**
> Find all students who has a marks of first subject = 90 
```
    db.students.find({"marks.0": 90})
```

**elemMatch**
> Find all students who have at least one subject having marks >= 90
```
    db.students.find({marks: {$elemMatch: {$gte: 90}}})
```

**Array size**
> Find all students whose marks array has a length of 4 
```
    db.students.find({marks: {$size: 4}})
```

##### 3. UPDATE OPERATION 
---------------------------------------

###### 1. **UpdateOne**
* Update one documents at once
```
    db.collectionName.updateOne(
        {filter statements},
        {
            $set:
                {fieldName: "newValue"}
        }
    )
```

###### 2. **UpdateMany**
* Update many documents at once
```
    db.collectionName.updateMany(
        {filter statements},
        {
            $set:
                {fieldName: "newValue"}
        }
    )
```

###### Example 

* Update students who have major "Biology" to "Bio"
```
    db.students.updateMany(
        {
            major:"Biology"
        },
        {
            major: "Bio"
        }
    )
```

###### 3. **replaceOne/ replaceMany**

```
    db.students.[replaceMany, replaceOne](
        {
            filter statements
        },
        {
            replaced documents 
        }
    )
```




##### 4. DELETE OPERATION 
---------------------------------------

###### **deleteOne/ deleteMany**
```
    db.students.[deleteMany, deleteOne](
        {
            filter statements
        }
    )  
```

###### Drop Collection 
```
    db.collectionName.drop()
```


#### MISCELANEOUS OPERATION 
--------------------------------------

###### **bulkWrite**

Do multiple operations on students collections
It is only possible on one collection
```
   db.students.bulkWrite(
      [
          {
              insertOne:
              {
                  "document" : {name: "Andrew", major: "Architecture", gpa: 3.2}
              }
          },
          {
              insertOne:
              {
                  "document": {name: "Terry", major: "Math", gpa: 3.7}
              }
          },
          {
              updateOne:
              {
                  filter: {name: "Terry"},
                  update: {$set: {gpa: 4.0}}
              }
          },
          {
              deleteOne:
              { filter: {name: "Kate"} }
          },
          {
              replaceOne
              {
                  filter: {name: "Mike"},
                  replacement: {name: "Genny", major: "Counselling", gpa: 2.5}
              }
          }
      ] 
   ) 
```


#### TEXT INDEXING 
* MongoDB provides **text indexes** to support text search queries on string content
* **Text indexes** can include any field whose value is a string or an array of string elements


###### Syntax of creating index 

```
    db.collectionName.createIndex({ fieldName1: "text", fieldName2: "text", ...})
```
We can create text indexing on certain fields of a collections to allow searching over the contents of the database 

**Text Searching**
###### Searching text in the documents of a collection of a database 

```
    db.collectionName.find({
        $text: { $search : "Search Phrase"}
    })
```

**Text Score**
###### We can also rank the matched documents with a score of accuracy (and then sort them to get the most accurate documents)

```
    db.collectionName.find({
        $text: { $search: "Search Phrase" },
        score: { $meta: "textScore"}
    ).sort({score: {$meta: "textScore"}})
```


#### AGGREGATION 
---------------------------------------------
* We can do some computataions on the stored documents 
to get certain values that are not stored 

Find out how many **toothbrush** were sold (using purchase_orders collections)

```
    db.purchase_orders.count({ product: "toothbrush"})
```


Find the distinct products 

```
    db.purchase_orders.distinct("product")
```

Find the total amount of money spent by each customer 

```
    db.purchase_orders.aggregate(
        [
            // On all documents
            {$match: {}},

            // Format the output
            {$group: {_id: "$product", total: {$sum : "$total"}}},

        ]
    )
```

Find how much has been spent on each product and sort them by 
descending order

```
    db.purchase_orders.find(
        [
            {$match" {}},

            {$group: {_id: "$product", total: {$sum: "$total"}}},

            {$sort : {total: -1}}
        ]
    )
```

Find how much money each customer has spent on toothbrushes &
pizza

```
    db.purchase_orders.aggregate(
        [
            {$match: {product: {$in : ["toothbrush", "pizza"]}}},
            {$group: {_id: "$product". total: {$sum: "$total"}}}
        ]
    )
```






