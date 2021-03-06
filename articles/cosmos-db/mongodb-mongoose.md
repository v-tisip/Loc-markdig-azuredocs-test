---
title: Using the Mongoose framework with Azure Cosmos DB | Microsoft Docs
description: Learn how to connect a Node.js Mongoose app to Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: romitgirdhar
manager: jhubbard
editor: ''

ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload:
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh

---
# Azure Cosmos DB: Using the Mongoose framework with Azure Cosmos DB

This tutorial demonstrates how to use the [Mongoose Framework](http://mongoosejs.com/) when storing data in Azure Cosmos DB. We use the MongoDB API for Azure Cosmos DB for this walkthrough. For those of you unfamiliar, Mongoose is an object modeling framework for MongoDB in Node.js and provides a straight-forward, schema-based solution to model your application data.

Azure Cosmos DB is Microsoft's globally distributed multi-model database service. You can quickly create and query document, key/value, and graph databases, all of which benefit from the global distribution and horizontal scale capabilities at the core of Azure Cosmos DB.

## Prerequisites

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) version v0.10.29 or higher.

## Create an Azure Cosmos DB account

Let's create an Azure Cosmos DB account. If you already have an account you want to use, you can skip ahead to [Set up your Node.js application](#SetupNode). If you are using the Azure Cosmos DB Emulator, follow the steps at [Azure Cosmos DB Emulator](local-emulator.md) to set up the emulator and skip ahead to [Set up your Node.js application](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## Set up your Node.js application

>[!Note]
> If you'd like to just walkthrough the sample code instead of setup the application itself, clone the [sample](https://github.com/Azure-Samples/Mongoose_CosmosDB) used for this tutorial and build your Node.js Mongoose application on Azure Cosmos DB.

1. To create a Node.js application in the folder of your choice, run the following command in a node command prompt.

    ```npm init```

    Answer the questions and your project will be ready to go.

2. Add a new file to the folder and name it ```index.js```.
3. Install the necessary packages using one of the ```npm install``` options:
   * Mongoose: ```npm install mongoose --save```
   * Dotenv (if you'd like to load your secrets from an .env file): ```npm install dotenv --save```

     >[!Note]
     > The ```--save``` flag adds the dependency to the package.json file.

4. Import the dependencies in your index.js file.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

5. Add your Cosmos DB connection string and Cosmos DB Name to the ```.env``` file.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

6. Connect to Azure Cosmos DB using the Mongoose framework by adding the following code to the end of index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > Here, the environment variables are loaded using process.env.{variableName} using the 'dotenv' npm package.

    Once you are connected to Azure Cosmos DB, you can now start setting up object models in Mongoose.

## Caveats to using Mongoose with Azure Cosmos DB

For every model you create, Mongoose creates a new MongoDB collection underneath the covers. However, given the per-collection billing model of Azure Cosmos DB, it might not be the most cost-efficient way to go, if you've got multiple object models that are sparsely populated.

This walkthrough covers both models. We'll first cover the walkthrough on storing one type of data per collection. This is the defacto behavior for Mongoose.

Mongoose also has a concept called [Discriminators](http://mongoosejs.com/docs/discriminators.html). Discriminators are a schema inheritance mechanism. They enable you to have multiple models with overlapping schemas on top of the same underlying MongoDB collection.

You can store the various data models in the same collection and then use a filter clause at query time to pull down only the data needed.

### One collection per object model

The default Mongoose behavior is to create a MongoDB collection every time you create an Object model. This section explores how to achieve this with MongoDB for Azure Cosmos DB. This method is recommended with Azure Cosmos DB when you have object models with large amounts of data. This is the default operating model for Mongoose, so, you might be familiar with this, if you're familiar with Mongoose.

1. Open your ```index.js``` again.

2. Create the schema definition for 'Family'.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

3. Create an object for 'Family'.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

4. Finally, let's save the object to Azure Cosmos DB. This creates a collection underneath the covers.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

5. Now, let's create another schema and object. This time, let's create one for 'Vacation Destinations' that the families might be interested in.
   1. Just like last time, let's create the scheme
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   2. Create a sample object (You can add multiple objects to this schema) and save it.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

6. Now, going into the Azure portal, you notice two collections created in Azure Cosmos DB.

    ![Node.js tutorial - Screen shot of the Azure portal, showing an Azure Cosmos DB account, with multiple collection names highlighted - Node database][mutiple-coll]

7. Finally, let's read the data from Azure Cosmos DB. Since we're using the default Mongoose operating model, the reads are the same as any other reads with Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### Using Mongoose discriminators to store data in a single collection

In this method, we use [Mongoose Discriminators](http://mongoosejs.com/docs/discriminators.html) to help optimize for the costs of each Azure Cosmos DB collection. Discriminators allow you to define a differentiating 'Key', which allows you to store, differentiate and filter on different object models.

Here, we create a base object model, define a differentiating key and add 'Family' and 'VacationDestinations' as an extension to the base model.

1. Let's set up the base config and define the discriminator key.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

2. Next, let's define the common object model

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

3. We now define the 'Family' model. Notice here that we're using ```commonModel.discriminator``` instead of ```mongoose.model```. Additionally, we're also adding the base config to the mongoose schema. So, here, the discriminatorKey is ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

4. Similarly, let's add another schema, this time for the 'VacationDestinations'. Here, the DiscriminatorKey is ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

5. Finally, let's create objects for the model and save it.
   1. Let's add object(s) to the 'Family' model.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   2. Next, let's add object(s) to the 'VacationDestinations' model and save it.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

6. Now, if you go back to the Azure portal, you notice that you have only one collection called ```alldata``` with both 'Family' and 'VacationDestinations' data.

    ![Node.js tutorial - Screen shot of the Azure portal, showing an Azure Cosmos DB account, with the collection name highlighted - Node database][alldata]

7. Also, notice that each object has another attribute called as ```__type```, which help you differentiate between the two different object models.

8. Finally, let's read the data that is stored in Azure Cosmos DB. Mongoose takes care of filtering data based on the model. So, you have to do nothing different when reading data. Just specify your model (in this case, ```Family_common```) and Mongoose handles filtering on the 'DiscriminatorKey'.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

As you can see, it is easy to work with Mongoose discriminators. So, if you have an app that uses the Mongoose framework, this tutorial is a way for you to get your application up and running on the MongoDB API on Azure Cosmos DB without requiring too many changes.

## Clean up resources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## Next steps

Learn more about the MongoDB operations, operators, stages, commands and options supported by the Azure Cosmos DB MongoDB API in [MongoDB API support for MongoDB features and syntax](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png