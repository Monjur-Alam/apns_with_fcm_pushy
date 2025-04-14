# 📲 APNs with FCM & Pushy Integration Guide for iOS Push Notifications

This guide walks you through integrating **Apple Push Notification service (APNs)** with **Firebase Cloud Messaging (FCM)** for iOS apps.

---

[📲 APNs with FCM Integration Guide for iOS Push Notifications](#apns-with-fcm-integration-guide-for-ios-push-notifications)

[🔧 Step-by-Step Guide](#-step-by-step-guide)
  1. [Apple Developer: Generate APNs Authentication Key .p8](#-1-apple-developer-generate-apns-authentication-key-p8)
  2. [Firebase Console Setup](#-2-firebase-console-setup)
  3. [iOS Project Setup (Xcode)](#-3-ios-project-setup-xcode)
  4. [Testing Push Notifications](#-4-testing-push-notifications)
  5. [Role or Permissions Issue](#-5-role-or-permissions-issue)

[What is Pushy?](#what-is-pushy)

[🔧 Prerequisites](#-prerequisites)

[🛠️ Steps to Configure APNs with Pushy on iOS](#%EF%B8%8F-steps-to-configure-apns-with-pushy-on-ios)
  1. [Upload .p8 Key to Pushy Dashboard](#-1-upload-p8-key-to-pushy-dashboard)
  2. [Integrate Pushy iOS SDK](#-2-integrate-pushy-ios-sdk)
  3. [Register Device for Push Notifications](#-3-register-device-for-push-notifications)
  4. [Handle Incoming Notifications](#-4-handle-incoming-notifications)
 

---

## APNs with FCM Integration Guide for iOS Push Notifications

## 🔧 Step-by-Step Guide

### ✅ 1. Apple Developer: Generate APNs Authentication Key (`.p8`)

1. Go to your [Apple Developer Account](https://developer.apple.com/account).
2. Navigate to **Certificates, Identifiers & Profiles > Keys**.
3. Click the **➕** button to create a new key.
4. Name the key and check **Apple Push Notifications service (APNs)**.
5. Click **Continue** and then **Register**.
6. Download the `.p8` file (you can **only download it once**!).
7. Note the following details:
   - **Key ID** (e.g., `ABCD1234`)
   - **Team ID** (found in the **Membership** section)

---

### ✅ 2. Firebase Console Setup

1. Go to the [Firebase Console](https://console.firebase.google.com/).
2. Select your project.
3. Ensure your project has an **iOS app** added.
   - If not, click the **iOS icon** in the project overview to register one.
   - Enter your **iOS bundle ID**.
   - Download and add `GoogleService-Info.plist` to your Xcode project.
4. Go to **Project Settings ⚙️ > Cloud Messaging** tab.
5. Scroll to the **APNs Authentication Key** section and click **Upload**.
6. Upload the following:
   - `.p8` File
   - **Key ID**
   - **Team ID**
7. Click **Save**.

---

### ✅ 3. iOS Project Setup (Xcode)

1. Enable:
   - **Push Notifications**
   - **Background Modes > Remote notifications** under **Signing & Capabilities**
2. Add Firebase SDK (via CocoaPods or Swift Package Manager).
3. Configure Firebase in `AppDelegate.swift`:

```swift
import Firebase
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        FirebaseApp.configure()
        return true
    }

    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        Messaging.messaging().apnsToken = deviceToken
    }
}
```
4. Request notification permissions:
```swift
UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) { granted, error in
    if granted {
        DispatchQueue.main.async {
            UIApplication.shared.registerForRemoteNotifications()
        }
    }
}
```
### ✅ 4. Testing Push Notifications
- Go to Firebase Console > Cloud Messaging > Send test message
- Use your device's FCM token for testing

---

### ✅ 5. Role or Permissions Issue

Make sure your Firebase user role has **Editor** or **Owner** access — **not just Viewer** or limited roles.

Even if you have "developer access", it might not be enough to upload the APNs key.

#### To check your access level:

1. Go to the [Google Cloud Console IAM](https://console.cloud.google.com/iam-admin).
2. Find your email in the list of project members.
3. Ensure your role is one of the following:
   - **Firebase Admin**
   - **Editor**
   - **Owner**

---

## ⚠️ Important Notes

- 🔒 `.p8` file is **sensitive** — store it securely and never commit it to version control.
- 📁 The file **does not expire**, but if deleted, you must generate a **new one** from Apple Developer portal.
- 🧾 Make sure your **Apple Developer account is active and valid**.
- 🔑 Your **Firebase user must have Editor or Owner permissions** to upload the `.p8` key.

---

## What is Pushy?

Pushy is an alternative push notification gateway primarily used for Android devices. However, Pushy now supports iOS via APNs using .p8 authentication keys.

---

### 🔧 Prerequisites
1. Apple Developer Account

2. Your iOS app with Pushy SDK installed

3. APNs Auth Key (.p8 file)

4. Pushy backend SDK (Node.js, Java, PHP, etc.)

---

### 🛠️ Steps to Configure APNs with Pushy on iOS
### 🔹 1. Upload .p8 Key to Pushy Dashboard
1. Go to Pushy Dashboard

2. Select your project

3. Go to "iOS Configuration"

4. Upload:

- .p8 file

- Key ID

- Team ID

- Bundle ID

You can find this info in your Apple Developer Account under Certificates, IDs & Profiles > Keys

---

### 🔹 2. Integrate Pushy iOS SDK
Use CocoaPods or Swift Package Manager:

With CocoaPods:

```swift
pod 'Pushy'
```

Then: 

```swift
import Pushy
```

---

### 🔹 3. Register Device for Push Notifications

```swift
import UIKit
import Pushy

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Register the device with Pushy
        Pushy.register { (deviceToken, error) in
            if let error = error {
                print("Pushy registration failed: \(error)")
                return
            }

            // Print device token or send it to your server
            print("Pushy device token: \(deviceToken)")
        }

        return true
    }
}
```

---

### 🔹 4. Handle Incoming Notifications

```swift
func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
    Pushy.handlePush(userInfo)
}
```

---

### 🚀 Sending Push from Server
Use Pushy backend SDK (e.g., Node.js):

```javascript
const Pushy = require('pushy');

// Your Pushy Secret API Key
const pushyAPIKey = 'your-secret-api-key';

const data = {
  message: 'Hello iOS via Pushy + APNs!'
};

const to = 'DEVICE_TOKEN_HERE';

Pushy.sendPushNotification(data, to, {}, pushyAPIKey, function (err, id) {
  if (err) {
    return console.log('Push send failed:', err);
  }

  console.log('Push sent successfully! ID:', id);
});
```

---

### ✅ Notes:
- Make sure your APNs key is active in Apple Dev Console.

- Device token must be stored securely on your backend.

- You can test push with Pushy's test console too.

---



