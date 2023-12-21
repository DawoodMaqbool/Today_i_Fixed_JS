## Problem
...
If you want to fetch the data on the creation of a new record in firestore database then you can use the following document.
...

## Environment
... Visual Studio Code, NodeJS ...

## How you fix it
...  In the .env file we place the Access Token and the database link. After the setup is completed on google cloud platform. we can use the document name and snapshot function. this script will automatically run when a new record is created in the firestore database.
...

## Solution
```
const functions = require("firebase-functions");
const admin = require("firebase-admin");

admin.initializeApp();

const firestore = admin.firestore();

// Cloud Function triggered on the creation of a new record in Fitness plan Table
exports.FetchDataOfNewlyCreatedDocument = functions.firestore
  .document("fitness_plan/{id}")
  .onCreate(async (snapshot, context) => {
    try {
      const FitnessPlanData = snapshot.data();

      const uId = FitnessPlanData.uid;
      const userId = uId._path.segments[1];
      const from_date = FitnessPlanData.from_date;

      console.log("User ID:", userId, "Publishing Date:", from_date);
      
    } catch (error) {
      console.error('Error Fetching Data from firestore database:', error);
    }
  });

```
