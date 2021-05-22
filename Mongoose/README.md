#### Mongoose Introduction
* Mongoose is a library of Node.js, it provides interaction with MongoDB using  Object-Relation Mapping
* Mongoose provides a straight-forward, schema-based solution to model our application data
* It includes built-in type casting, validation, query building, business logic hooks and more



#### TERMINOLOGIES OF MONGOOSE 

##### 1. SCHEMA 

* Schema Creation will create MongoDB collection & defines the shape or structure of the documents within the collection
* If we want to create collection similar to SQL tables we can use schema 
**IN-FACT:** Schema creation is similar to Table Creation in SQL  
* In Schema Creation we will specify the details of field   like "field name", its "type", "default value" if needed, "index concept" when needed, "constraint concept" when needed  
       

**NOTES:**
   * A Schema can contain static, non-static(instance) methods 
   * It can also contain indexes 
        
**EXAMPLE** 
    
```js
      var userSchema = new Schema({
         userID: String, 
         chips: {type: double, default: 0.0},
         regDate: {type: Date, default: Date.now,required: true} 
      })
```

##### 2. MODEL 

* A model **defines a programming interface for interacting with the database** (read, insert, update, etc)
* Model is an **object that gives us easy access to a named collection, allowing you to query the collection and use the Schema to validate any documents we save to that collection** 
* It is created by combining a Schema, a Connection, and a collection name
* An instance of a model is called a document
* Models are responsible for creating and reading documents from the underlying MongoDB database   


###### **MODEL CREATION**
SYNTAX
```
      var modelName = mongoose.model('collectionName', schemaName);
```
Model Creation For  'userSchema'
```js
      var userModel = mongoose.model('users', userSchema);
```

###### **Create Document Instances using model's**

SYNTAX 
```
   var documentInstance = new modelInstance(values for schema)
```

EXAMPLE

```js
   var santosh = new userModel({userid: 'Santosh', chips: 66171, regDate: Date.now})

   // rest field values will be filled with defaults
   var subhash = new userModel({usertid: 'subhash'})
```


##### KEY METHODS & PROPERTIES FOR MONGOOSE 

###### 1. connect() 

* It opens the default mongoose connection

SYNTAX 
```
   connect(uri(s), [options], [callback])
```

EXAMPLE 

```js

   var mongoose = require('mongoose');


   mongoose.connect("mongodb://mongodb.tutorialtous.com/mongoose",function(error){
      if(error){
         console.log("error"+error);
      }else{
         console.log("open done")
      }
   });
```

NOTES:
* Once we call this method it will call Connection’s open() or openSet() method
* When connection succeed, ‘connection’ property is set with the opened connection
* It means ‘mongoose.connection’ will holds connection information




###### 2. createConnection()
   * It will create an instance of connection & its work is similar to connect() 
    
SYNTAX
```
      createConnection([uri], [options], [options.config], [options.config.autoIndex])
``` 

NOTES:
* All arguments are optional
* Returns **mongoose.Db** property 
* If have **uri** & **other arguments**, it will calls **connection.open()** or **connection.openSet()**
* If we doesn't pass any arguments we can use returned property i.e. 
**mongoose.Db**'s **open()**, **openSet()** methods to open connections 

EXAMPLE 

```js

   var mongoose = require('mongoose');

   // with arguments
   var db =mongoose.createConnection(
      "mongodb://mongodb.tutorialtous.com/mongoose",
      function(error){
         if(!error){
            console.log("open connection done");
         }
      }
   );


   // Without arguments 
   var db = mongoose.createConnection();
   db.open("mongodb://mongodb.tutorialtous.com/mongoose",
         function(err){
               if(!error){
                  console.log("Open Connection done");
               }
         } 
   )


```


###### 3. Schema()

* Mongoose Schema constructor 

EXAMPLE

```js
   var mongoose = require('mongoose');
   var mySchema = mongoose.Schema;
   var schemaRef = new mySchema();
```

###### 4. Model()
* The Mongoose Model constructor

###### 5. Document()
* The Mongoose Document construtor


###### 6. disconnect()
* Disconnects all connections

SYNTAX 
```
   Disconnect([function])
```



##### MONGOOSE CONNECTION 

###### 1. open()
* Open the connection to MongoDB

SYNTAX 

```
   open(connection_string, [database], [port], [options], [callback])
```

SYNTAX DESCRIPTION 

Arguments | Type | Description
---|---|---
connection_string | String | **mongodb://uri** or the **host** to which we are connecting 
database | String | Database Name (Optional)
port | Number | Database port (Optional)
options | Object | Options (Optional) **Examples :** db, server, replset, user, pass, auth
callback | Function | (Optional)  


EXAMPLE 

```js
   var mongoose = require('mongoose');
   var connection = mongoose.createConnection();

   var callback = function(err){
      if(err)
         console.log(err);
      else
         console.log("Connection opened");
   }

   connection.open("mongodb://localhost/databaseName", callback);
```


###### 2. openSet()
* Opens the connection to replica set 

SYNTAX 

```
   openSet(uris, [database], [options], [callback])
```

SYNTAX DESCRIPTION 

Arguments | Type | Description 
---|---|---
uris | String | Comma-separated **mongodb://URIs**
database | String | Database name (if not included in uris)
options | Object | Passed to the internal driver 
callback | Function |  


**NOTE :** Functionality of openSet() will be similar to open() except openSet() works with replica sets 

EXAMPLE 

```js
   var mongoose = require('mongoose');
   var connection = mongoose.createConnection();

   var callback = function(err){
      if(err)
         console.log(err);
      else
         console.log("Connection opened");   
   }

   connection.openSet("mongodb://localhost/databaseName","mongodb://mongodb.databaseName.com:23396/databaseName",
   callback);
```

###### 3. close()
* It closes the connection
* It does not throw any error even if the connection is not 
opened or already closed 

SYNTAX 
```
   close([callbackFunction])
```

EXAMPLE 

```js
   var mongoose = require('mongoose');

   var connection = mongoose.createConnection();

   var callback = function(err){
      if(err)
         console.log(err);
      else
         console.log("Connection opened");   
   };

   var connectionCallback = function(err){
      if(err)
         console.log(err);
      else
         console.log("Connection opened");   
   };

   connection.open("mongodb://localhost/databaseName", callback);
   connection.close(connectionCallback);


```

###### readyState 
* It is the property of connection which holds state of the 
connection 

State  | Description 
---| ---
0 | disconnected 
1 | connected 
2 | connecting 
3 | disconnecting 

* **When will it be useful?**
   * Due to network interrupt at server/client side or server
     down time, connection to the database may lose, inorder 
     to trace those situations we need to use **readyState**
     concept 

   * EXAMPLE    

         ```js
            var mongoose = require('mongoose');
            var connection = mongoose.createConnection();

            var connecting = function(err){
               if(err)
                  console.error(err);
               else
                  console.log("Please wait connecting\t" + connection.readyState);   
            }

            var connected = function(err){
               if(err)
                  console.error(err);
               else
                  console.log("Connection connected\t" + connection.readyState);   
            }
             
            var disconnected = function(err){
               if(err)
                  console.error(err);
               else
                  console.log("Connection disconnected\t" + connection.readyState);   
            }

            var disconnecting = function(err){
               if(err)
                  console.error(err);
               else
                  console.log("Please wait disconnecting\t" + connection.readyState);   
            }

            connection.on("connecting", connecting);  
            connection.on("disconnecting", disconnecting);  
            connection.on("connected", connected);  
            connection.on("disconnected", disconnected);
              
            connection.open("mongodb://localhost/databaseName");
         ```  

##### MONGOOSE SCHEMA 

###### 1. Schema CONSTRUCTOR 
* It defines a schema with the definition 
* Definiton of the schema need to be in BSON form 
* **BSON Form :** {fieldName: {field options}}

SYNTAX 
```
   Schema(definition, [options])
```

**Some key options are :-**

Options | Type | Default 
---|---|---|---
autoIndex |  bool | null (Means use the connection's autoIndex option)
capped | bool | false 
collection | string | No default (Connection name that we need)
versionKey | string | "_v"


EXAMPLE 

```js
   var userSchema = new Schema({
      userid: {type: String, required: true, trim: true},
      chips: {type: Number}
   })
```

NOTES: 
   * Mongoose will add 's' to collection name (If collection="user" it will be saved as "users")
   * If we want custom naming(without 's') then use **collection option** as **new Schema({...}, {collection: user});** 

###### 2. add()
* It can be used to add field to a defined schema
  
SYNTAX 
```
   add(object, prefix)
```  

EXAMPLE 

For the userSchema we can add :- 
   * regdate - holds date of registration 
   * usertype - indicates user is player or promoter(agent)

   ```js
      userSchema.add({
         regdate: Date,
         usertype: {type: String, default: 'player'}
      });
   ```


###### 3. get()
* It can be used to know the value of particulat option of a schema 


SYNTAX 
```
   get(optionkey)
```

EXAMPLE 
```js
   // value of "capped" option of "userSchema"
   console.log(userSchema.get('capped'));

   // value of "autoIndex" option of "userSchema"
   console.log(userSchema.get('autoIndex'));
```


###### 4. set()
* It is used to set/get the option of a schema

SYNTAX 
```
   set(key, [value])
```

EXAMPLE 

```js
   
   // set the "capped" option of userSchema to "false"
   userSchema.set('capped', false);

   // set the "strict" option of userSchema to "false"
   userSchema.set('strict', false);
```

###### 5. index()

* It defines an index for the schema
* Compound index creation is possible using index() only (Which is not possible at schema definition(in schema() or add()))

SYNTAX 

```
   index(fields, [options], [options.expires=null])
```

**Note :** Here options refers to MongoDB index options like 'unique', 'name', 'expireAfterSeconds', etc.

EXAMPLE 

```js
   // create an index for the "userid" to "userSchema"
   userSchema.index({userid: 1}, {name: 'useridindex', unique: true});
```

**NOTE:** 
* statics are the methods defined on the **Model**
* methods are defined on the **document** (instance)

###### 6. method()

* It is used to add instance method to documents from Models compiled from this schema

SYNTAX 

```
   method(methodName, functionCode)
```

EXAMPLE 

```js

   // creating schema 
   var userSchema = new Schema({
      userid: {type:String, required: true, trim: true},
      chips: {type: Double}
   });


   // Adding "getInfo" instance method to the userSchema
   userSchema.method("getInfo", function(){
      console.log("userid:\t" + this.userid + "\n chips own:\t" + this.chip);
   });


   // Adding "getChips" instance method to the userSchema
   userSchema.method("getChips", function(){
      return this.chips;
   });


   // Calling those methods 
   
   // Creating model of the schema 
   var userModel = new mongoose.model('users', userSchema);

   // Creating a document (instance of model)
   var santosh = new userModel({userid: 'santosh', chips: 1000, regdate: Date.now});

   // calling "getInfo" method
   santosh.getInfo();

   // calling "getChips" method
   console.log(santosh.getChips());

```


###### 7. static()

* It is used to add "static" class methods to Models from this schema

SYNTAX 

```
   static(methodName, functionDefinition)
```

EXAMPLE 

```js
   // defining static method "getSchemaName"
   userSchema.static('getSchemaName', function(){
      return 'users';
   });

   // defining static method "saveDoc"
   userSchema.static("saveDoc", function(query){
      // codes
   })


   // Calling "getSchemaName" static method
   console.log(userSchema.getSchemaName());
```


##### MONGOOSE CRUD OPERATIONS


###### 1. save()
* Saves the document 

SYNTAX 
```
   save([options], [options.safe], [options.validateBeforeSave], [fn])
```

EXAMPLE 

```js
   var mongoose = require('mongoose');
 
   // create schema
   var userSchema = new Schema({
      userid: {type: String, required: true, index: true, unique: true},
      chips: {type: Number}
   });

   // create model
   var userModel = mongoose.model('users', userSchema);

   // create a document
   var santosh = new userModel({userid: 'Santosh', chips: 2000, })

   var callback = function(err){
      if(err)
         console.log("Connection Failed");
      else
         console.log("Connection opened");   
   }


   // connect to the database 
   mongoose.connect("mongodb://localhost/databaseName", callback);

   // save the document in the database 
   santosh.save((santosh, err) => {
      if(err)
         console.err(err);
      else
         console.log("Document saved successfully!");   
   });

```


###### 2. find()
* Find specific document from a collection/model


SYNTAX 
```
   find(conditions, [projection], [options], [callback])
```

NOTE: Conditions, projection, options are similar to mongodb's find() method


EXAMPLE
```js

   
   var echoRecords = (err,log) => {
      console.log("Total Records Found:"+log.length);

      for(var i=0; i<log.length; i++){
         console.log(
               (i+1) + "\t" +
               log[i]._id + "\t" +
               log[i].userid + "\t" +
               log[i].chips
         );
      }
   };


   // find all documents in userModel or collection
   userModel.find(echoRecords);

   // find all documents which have userid = "santosh"
   userModel.find({userid: 'santosh'}, echoRecords);
   
   // find first document which have userid = "santosh"
   userModel.find({userid: 'santosh'}, {_id: 0}, echoRecords);
   
```


###### 3. update()
* Updates documents in the database without returning them

SYNTAX
```
   update(conditions, doc, [options], [callback])
```


EXAMPLE

```js

   // Update chips of first doc which has a userid = "santosh" to 25000
   userModel.update({userid:"santosh"},{chips:25000},(err,log) => console.log("Number of Records Effected"+log));
   
   // Update chips of all docs which have a chips < 20000 to 35000
   userModel.update({chips:{$lt:20000}},{chips:35000},{multi:true}, (err,log) =>
   console.log("Number of Records Effected"+log));

```


###### 6. remove()

* Removes document(s) from the collection

SYNTAX
```
   remove(conditions, [callback])
```

* Note : If conditions not passed all records will be deleted 

EXAMPLES

Remove the user having userid = 'santosh'

```js
   userModel.remove({userid: 'santosh'});
```

Remove all records of the collection

```js
   userModel.remove();
```

###### 7. findOne()

* Finds a single document by its _id field


SYNTAX 
```
   findOne([conditions], [projection], [options], [callback])
```

EXAMPLE

Find the details of user having _id = "57971362517cdfd0260a638c"

```js
   userModel.findOne({_id: '57971362517cdfd0260a638c'}, "chips,userid", (err, data) => {
      if(!err)
         console.log(data);
   });
```


###### 8. findOneAndRemove()

* It issues a mongoDB findAndModify remove command 

SYNTAX 

```
   findOneAndRemove(conditions, [options], [callback])
```

EXAMPLE


Remove the user details having the id "57971362517cdfd0260a638c"

```js
userModel.findOneAndRemove({_id:"57971362517cdfd0260a638c"},
  (err,data)=> { 
     if(!err)
      console.log(data);
}); 
```

###### 9. findByIdAndUpdate()

* Finds a matching document, updates it according to the update tag and returns the found document(if any) to the callback 
* The query executes immediately if callback is passed else a Query object is returned 

SYNTAX

```
   findByIdAndUpdate(id, [update], [options], [callback])
```

EXAMPLE

Reset the chips for the id="hdhdddjfjfjfjf"

```js
   userModel.findByIdAndUpdate('hdhdddjfjfjfjf', {chips: 1000}, (err, data) => {
      if(!err)
         console.log(data);
   });
```


###### 10. findById()

* Finds a single document by its _id field

SYNTAX 
```
   findById(id, [projection], [options], [callback])
```

EXAMPLE

Find the user details having id = "hfhjaehjjhoymdhv"

```js
   userModel.findById('hfhjaehjjhoymdhv', "chips,userid", (err, data) => {
      if(!err)
         console.log(data);
   })
```


###### 11. findByIdAndRemove()

* Issue a mongodb findAndModify remove command by a document's _id field
*  **findByIdAndRemove(id, ...)** is equivalent to **findOneAndRemove({ _id: id }, ...)**

SYNTAX 

```
   findByIdAndRemove(id, [options], [callback])
```

EXAMPLE 

Remove the document having userid = 'hdvhfkjhuakbhbkjureu'

```js
   userModel.findByIdAndRemove('hdvhfkjhuakbhbkjureu', (err, data) => {
      if(!err)
         console.log(data);
   })
```

###### 12. count()

* Counts the number of matching documents in a database collection

SYNTAX 

```
   count(conditions, [callback])
```

EXAMPLES

Find number of users in users Schema 

```js
   userModel.count({}, (err, count) => {
      console.log('Total records in users schema: ' + count);
   });
```


Find number of users having chips <= 25000

```js
   userModel.count({chips : {$lte: 25000}}, (err, count) => {
      console.log('Total users having chips <= 25000' + count);
   });
```

###### 13. where()

* Used to filter out documents 

SYNTAX 
```
   where(path, [val])
```

EXAMPLES 

Find the users having chips < 10000
```js
   userModel.where('chips').lt(10000);
```

Find the users of country 'India' and have chips < 10000

```js
   userModel.where('chips').lt(10000).where('country').eq('India');
```



