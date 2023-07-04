## Problem

...
How to trigger dynamic templates in firebase notifications

## Environment

... Visual Studio Code, NodeJS/NestJs ...

## How you fix it

... Create a helper method for converting your template dynamic data with the actual values before sending it to firebase. This method is based on the pattern that you are storing the dynamic data in the template like {{name}}

Template Example:
template = 'Welcome {{user.name}} to Virtual Force'

## Solution

```
import admin from 'firebase-admin';

// Initialize firebase
public async sendFirebaseNotification(deviceToken: string, payload: any) {
  const options = {
    sound: 'default',
    alert: true,
    priority: 'high',
    timeToLive: 60 * 60,
  };
  try {
    const response = await admin
      .messaging()
      .sendToDevice(deviceToken, payload, options);

    console.log('Notification sent successfully', response);
  } catch (error) {
    throw new Error(
      'Not able to send the firebase notification',
    );
  }
}

public async sendNotification() {
  const template = 'Welcome {{user.name}} to Virtual Force'
  const data = {
    user:{
      name: 'saud'
    }
  }
  const notificationText = setNotificationText(template, data); // This will come out as  'Welcome saud to Virtual Force'

  const notificationPayload = {
      notification: {
        title: 'Notification',
        body: notificationText,
        sound: 'default',
      },
      data: {}, // Extra info you want to send with the notification
    };

  await this.fireBaseService.sendNotification(deviceToken, notificationPayload);
}

public function setNotificationText(template: string, data: DataType): string {
  let str = template;
  str = str.replace(/(<([^>]+)>)/gi, '');

  [...str.matchAll(/\{{(.*?)\}}/g)].forEach((match) => {
    const [tableName, columnName] = match[1].split('.');
    str = str.replace(/\{{(.*?)\}}/, data[tableName][columnName]);
  });

  return str;
}
```
