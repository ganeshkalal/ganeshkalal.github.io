![](https://cdn-images-1.medium.com/max/1000/1*VRWqHJCmYtFCP6u0hassbg.jpeg)
<span class="figcaption_hack">Image by [TeroVesalainen](https://pixabay.com/en/users/TeroVesalainen-809550/)
at [Pixabay](https://pixabay.com/photo-2480959/)</span>

# Yet another tutorial of Push Notifications on Android! — Part 1

Push Notifications on smartphones has grown out to be one of the essential
feature for almost every app that comes out on Play Store. Hence it is quite an
important thing to learn for every mobile developer and i am sure by now most of
us are already well versed with integrating FCM Notifications on Android. But
for those who have just started developing apps on android or those who just
want to brush up on basics, this post will help you.

I will be covering this topic step by step in Java (as opposed to Kotlin to make
it easier for beginners) and this post will be the **Part 1** covering android
side of FCM Notifications. **Part 2** will be covering the basic server side
implementation on Python.

### Step 1 : Create a new project in Android Studio

Boot up android studio on your laptop/desktop and create a new android project.
Once created, head over to your app-level build.gradle and add following line :

    implementation 'com.google.firebase:firebase-messaging:17.3.4'

Now head over to your project’s *AndroidManifest.xml *and add permission for
Internet.

    <uses-permission 
    "android.permission.INTERNET"/>

### Step 2: Download google-services.json

Login in Firebase Console with your Google Account. After logging in, create a
new project by providing a suitable name. Once the project is created, click on
add android app, provide your project’s package name (that we created in step 1)
and click on register app.

Once registered, download the generated config file (google-services.json). Copy
this config file to your project’s app directory.

> Tip : Switch to project view in AndroidStudio, click on project name and paste
> the copied config file

Now add below line to your project-level build.gradle:

    classpath 'com.google.gms:google-services:4.2.0'

### Step 3: Create our own FirebaseMessagingService

Create a new java file named as *MyFirebaseMessagingService.java *and* *extend
FirebaseMessagingService. This service will take care of receiving & sending
messages or receiving data payload. Add following code in this java file :

    @Override
    onNewToken(String token) {
      Log.
    (TAG, "Refreshed token: " + token);

     // If you want to send messages to this application instance or
     // manage this apps subscriptions on the server side, send the
     // Instance ID token to your app server.
     sendTokenToServer(token);
    }

Above code will log a token when the app is first initialized in your
smartphone. This token is provided by FCM Server and should ideally be stored at
your server side. Server side will use this token to push any notification to
the device provided the token used is right.

> Note :- Be sure to save this token at your server every time the token is
> refreshed

Now add this service in your *AndroidManifest.xml*

    <service 
    ".MyFirebaseMessagingService">
      <intent-filter>
        <action 
    "com.google.firebase.MESSAGING_EVENT"/>
      </intent-filter>
    </service>

### Step 4: Receive Notification Messages

To receive notification messages, we will have to override
*onMessageReceived().* Add below code to *MyFirebaseMessagingService.java :*

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        Log.d(TAG, "From: " + remoteMessage.getFrom());
        if (remoteMessage.getData().size() > 0) {
            Log.d(TAG, "Message data payload: " + remoteMessage.getData());
        }
        if (remoteMessage.getNotification() != null) {
            Log.
    (TAG, "Message Notification Title: " + remoteMessage.getNotification().getTitle());

            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
    }

Above code prints out *title, body, data and from *if any *remoteMessage* is
received.

### Step 5: Display received message as a Notification

In order to use *NotificationCompat, we need to add following dependency*


Modify your onMessageReceived() method that we created in **Step 4** as below :

    @Override
    onMessageReceived(RemoteMessage remoteMessage) {
    Log.
    (TAG, "From: " + remoteMessage.getFrom());
    (remoteMessage.getData().size() > 0) {

        Log.
    (TAG, "Message data payload: "+remoteMessage.getData());

      }
    (remoteMessage.getNotification() != 
    ) {

        Log.
    (TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());

        Log.
    (TAG, "Message Notification Title: " + remoteMessage.getNotification().getTitle());
        createNotificationChannel();
        NotificationCompat.Builder mBuilder = 
    NotificationCompat.Builder(
    , CHANNEL_ID)
            .setSmallIcon(R.drawable.ic_launcher_foreground)
            .setContentTitle(remoteMessage.getNotification().getTitle())
            .setContentText(remoteMessage.getNotification().getBody())
            .setPriority(NotificationCompat.PRIORITY_DEFAULT);
        NotificationManagerCompat notificationManager = NotificationManagerCompat.
    (
    );
        
    notificationManager.notify(NOTIFICATION_ID, mBuilder.build());
      }
    }

Now we need to create a notification channel if we are building apps for
versions greater than or equal to Android Oreo. Add below code after
*onMessageReceived* method :

    createNotificationChannel() {
      
    (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
          
    importance = NotificationManager.IMPORTANCE_DEFAULT;

    NotificationChannel channel = 
    NotificationChannel(CHANNEL_ID, CHANNEL_ID, importance);

    NotificationManager notificationManager = getSystemService(NotificationManager.
    );
          notificationManager.createNotificationChannel(channel);
      }
    }

Note : CHANNEL_ID is a constant string & NOTIFICATION_ID is just a constant
integer. You can use any string and integer here.

### Step 6: Finishing Touches

We can add a default icon and color for our notification which will be displayed
in case we don’t specify it while building notification in **Step 5**. Add below
code in your *AndroidManifest.xml :*

    <meta-data
        
    "com.google.firebase.messaging.default_notification_icon"
        
    "@drawable/ic_launcher_foreground"/>

    <meta-data
        
    "com.google.firebase.messaging.default_notification_color"
        
    "@android:color/white"/>

### Step 7 : Test Your App

It’s time we test our app. Run your app into a physical device or on our
emulator. Open up Logcat while your app is getting installed onto your device.
Once installed, firebase will be initialized and a token will be generated and
printed on your logcat. Copy this logcat in a text file. We will need this token
to test notification on our device.

Now head over to Firebase Console and click on Cloud Messaging section. Here
click on create a new notification, add Notification Title & Text. Once done,
test on device and enter the token we saved above.

Voila! You should now receive a notification with the entered title and text.

### To Be Continued…

Congrats! You have just implemented notifications on Android. If you have any
questions or suggestions, please post them below. To view the entire code,
follow below link :

[Github repo for code](https://github.com/ganeshInnoctive/fcm-demo)

I appreciate the effort you took to read my first post. In the Part 2 of this
series, i will cover the server side implementation in python.

### Follow me at Medium - [Ganesh Kalal](https://medium.com/@gkalal64)
