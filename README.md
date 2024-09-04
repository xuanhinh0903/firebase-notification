# firebase push notification

This is a simple project to send notifications to a Firebase Cloud Messaging (FCM) server.

## Getting Started

### Prerequisites

- react.js
- Firebase CLI

### Installation

1. Create a new Firebase project and enable Firebase Cloud Messaging.

- Go to console
- Create a project
- select option web app
  ![alt text](<img src="select-web-app.png">)

Okey, now you have a project.

2. Install firebase

- npm install firebase

3. Set up firebase to your project

- In folder public create a file called firebase-messaging-sw.js
  =>> Initialize and check if a message is received; if so, display it on the browser.
  importScripts('https://www.gstatic.com/firebasejs/9.0.0/firebase-app-compat.js');
  importScripts('https://www.gstatic.com/firebasejs/9.0.0/firebase-messaging-compat.js');

const firebaseConfig = {
apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
storageBucket: process.env.NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET,
messagingSenderId: process.env.NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
appId: process.env.NEXT_PUBLIC_FIREBASE_APP_ID,
measurementId: process.env.NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID,
};

const messaging = firebase.messaging();

messaging.onBackgroundMessage((payload) => {
console.log('Received background message ', payload);
const notificationTitle = payload.notification.title;
const notificationOptions = {
body: payload.notification.body,
icon: '/firebase-logo.png',
};

self.registration.showNotification(notificationTitle, notificationOptions);
});

- create file firebase.ts

=>> config firebase get in Project settings

export const firebaseConfig = {
apiKey: process.env.NEXT_PUBLIC_FIREBASE_API_KEY,
authDomain: process.env.NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN,
projectId: process.env.NEXT_PUBLIC_FIREBASE_PROJECT_ID,
storageBucket: process.env.NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET,
messagingSenderId: process.env.NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID,
appId: process.env.NEXT_PUBLIC_FIREBASE_APP_ID,
measurementId: process.env.NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID,
};

in file use need get message, use to code:

useEffect(() => {
// This function registers the service worker for Firebase Cloud Messaging
//check if service worker is supported
const registerServiceWorker = async () => {
if ('serviceWorker' in navigator) {
try {
// Attempt to register the service worker
const registration = await navigator.serviceWorker.register('/firebase-messaging-sw.js');
return registration;
} catch (err) {
console.error('Service Worker registration failed:', err);
return null;
}
}
return null;
};

    // This function sets up Firebase Cloud Messaging
    const setupFirebaseMessaging = async () => {
      // Initialize Firebase app
      const app = initializeApp(firebaseConfig);
      // Get messaging instance
      const messaging = getMessaging(app);

      // Register service worker
      const swRegistration = await registerServiceWorker();
      if (!swRegistration) {
        console.error('Service Worker registration failed. Cannot setup Firebase Messaging.');
        return;
      }

      try {
        // Request notification permission
        const permission = await Notification.requestPermission();
        if (permission === 'granted') {
          // Get FCM token
          const currentToken = await getToken(messaging, {
            vapidKey: 'BJtq8rVCbuF7zqAVcNccdQX5N-JfhbxKiHyix2uKVNUXpcFDsi9W9JHgRfjvq-TyGJ-_1r1lHItAvgZPNbOhs_o',
            serviceWorkerRegistration: swRegistration,
          });

          if (currentToken) {
            console.log('FCM token:', currentToken);
          } else {
            console.log('No registration token available.');
          }
          // Set up message handler for foreground messages
          onMessage(messaging, (payload) => {
            alert(payload);
          });
        }
      } catch (err) {
        console.error('An error occurred while retrieving token. ', err);
        if (err instanceof Error) {
          console.error('Error details:', err.message);
        }
      }
    };

    // Call the setup function
    setupFirebaseMessaging();

}, []); // Empty dependency array means this effect runs once on mount

- Don't forget to add firebaseConfig to .env
  NEXT_PUBLIC_FIREBASE_API_KEY=AIzaSyAsPY48Im-NNzjBVPxnTvGQXKSF5pPMXo8
  NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=impact-labs-dab4e.firebaseapp.com
  NEXT_PUBLIC_FIREBASE_PROJECT_ID=impact-labs-dab4e
  NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET=impact-labs-dab4e.appspot.com
  NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID=986138503789
  NEXT_PUBLIC_FIREBASE_APP_ID=1:986138503789:web:5ed2b8630d89a0e4fcad64
  NEXT_PUBLIC_FIREBASE_MEASUREMENT_ID=G-4ZZ9W419CD
  NEXT_PUBLIC_VAPID_KEY= BPTI4HqOWzPPNVcFWrEnnhPaJ04fAXteY-lI_jXRE9OQBC3A0onvDFew0nBuEaxviUgXv62KY1cHl0Id2ZO

- if setup success, you can see token in console, move token to run/message in column right =>> create your first campaign =>> pass token to "Add an FCM registration token" =>> send test notification"

For now, you can see notification in notification bar of your browser
