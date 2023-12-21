## Problem
...
If you want to send notification using fcm on the creation of a new record in firestore database then you can use the following document.
...

## Environment
... Visual Studio Code, NodeJS ...

## How you fix it
...  Assuming that the setup is completed on google cloud platform. we fetch data from one table and extract the user id against which the record was created. In this sceanrio we are publishing nytrition plans for our clients. Once we get the user id then we compare it with ids from users table and extract the relevant fcm token which is placed inside a subcollection in users collection. Then we move onto create a template and use sendall functionality to send notifications to the users.
...

## Solution
```
const functions = require("firebase-functions");
const admin = require("firebase-admin");

admin.initializeApp();

const firestore = admin.firestore();

// Cloud Function triggered on the creation of a new nutrition plan
exports.scheduleFitnessPlanNotifications = functions.firestore
  .document("fitness_plan_temp/{id}")
  .onCreate(async (snapshot, context) => {
    try {
      const FitnessPlanData = snapshot.data();

      const uId = FitnessPlanData.uid;
      const userId = uId._path.segments[1];
      const from_date = FitnessPlanData.from_date;
      const to_date = FitnessPlanData.to_date;

      const userRef = await firestore.collection('users').doc(userId);

      const userDoc = await userRef.get();

      const messages = [];

      if (userDoc.exists) {
        // const userData = userDoc.data();
        
        const fcmTokensRef = userDoc.ref.collection('fcm_tokens');
        const fcmTokensSnap = await fcmTokensRef.get();

        if (!fcmTokensSnap.empty) {
          for (const doc of fcmTokensSnap.docs) {
            const fcmTokenData = doc.data();
            const message = {
              data: {
              title: 'Fitness Plan is Ready',
              body: `Your Fitness Plan is Ready and you can start from ${from_date} till ${to_date}.`,
              },
              token: fcmTokenData.fcm_token,
            };
            messages.push(message);
          }
          
          const messaging = admin.messaging();
          await messaging.sendAll(messages)
          .then((response) => {
            // Messages sent successfully
            console.log('Notifications sent successfully for all tokens.');
            })
            .catch((error) => {
              console.error('Error sending notifications:', error);
              });
        } else {
          console.log('No FCM tokens found for this user.');
        }
      } else {
        console.log('User with ID', userId, 'does not exist.');
      }

    } catch (error) {
      console.error('Error Sending Nutrition Plan related notifications:', error);
    }
  });
```
