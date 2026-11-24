# MedCycle — Setup Guide

## ✅ What's included in this ZIP

| Module | Status |
|---|---|
| Project Gradle setup | ✅ Complete |
| AndroidManifest | ✅ Complete |
| Data Models (User, Medicine, Chat, Emergency) | ✅ Complete |
| Firebase Repositories (Auth, Medicine, Chat, User, Emergency) | ✅ Complete |
| Auth (Login, Signup, Google Sign-In, Phone OTP) | ✅ Complete |
| Home Screen (Search, Categories, Shimmer, SOS, Nearby) | ✅ Complete |
| Donate Medicine (Upload form, image, Firestore) | ✅ Complete |
| Marketplace (Sell/Donate filter tabs) | ✅ Complete |
| Chat System (Real-time, read receipts, typing) | ✅ Complete |
| Emergency SOS (Location, alert, quick call) | ✅ Complete |
| Maps Fragment (Google Maps, my location) | ✅ Complete |
| Profile (Edit, image upload, logout) | ✅ Complete |
| All XML Layouts | ✅ Complete |
| All Drawable vector icons | ✅ Complete |
| Navigation Graphs | ✅ Complete |

---

## 🚀 Step-by-Step Setup

### Step 1 — Open in Android Studio
1. Open Android Studio (Electric Eel or newer)
2. File → Open → select the `MedCycle` folder
3. Wait for Gradle sync to finish

### Step 2 — Create Firebase Project
1. Go to https://console.firebase.google.com
2. Click **Add project** → name it `MedCycle`
3. Click **Add Android app**
   - Package name: `com.medcycle`
   - Download `google-services.json`
4. Place `google-services.json` inside `/app/` folder

### Step 3 — Enable Firebase Services
In Firebase Console, enable:
- **Authentication** → Email/Password, Google, Phone
- **Firestore Database** → Start in test mode
- **Storage** → Start in test mode
- **Cloud Messaging** → (auto-enabled)

### Step 4 — Google Sign-In Setup
1. In Firebase Console → Authentication → Sign-in method → Google → Enable
2. Copy the **Web client ID**
3. Open `res/values/strings.xml`
4. Replace `YOUR_WEB_CLIENT_ID_HERE` with your actual Web client ID

### Step 5 — Google Maps Setup
1. Go to https://console.cloud.google.com
2. Enable **Maps SDK for Android**
3. Create an API key
4. Open `AndroidManifest.xml`
5. Replace `YOUR_GOOGLE_MAPS_API_KEY_HERE` with your API key

### Step 6 — Add Safe Args Plugin (for navigation)
In your **project-level** `build.gradle`, add to `dependencies {}`:
```groovy
classpath "androidx.navigation:navigation-safe-args-gradle-plugin:2.7.6"
```
In your **app-level** `build.gradle`, add to `plugins {}`:
```groovy
id 'androidx.navigation.safeargs.kotlin'
```

### Step 7 — Firestore Security Rules
In Firebase Console → Firestore → Rules, paste:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if request.auth != null;
      allow write: if request.auth.uid == userId;
    }
    match /medicines/{medicineId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update, delete: if request.auth.uid == resource.data.donorId;
    }
    match /chats/{chatId} {
      allow read, write: if request.auth.uid in resource.data.participants;
    }
    match /chats/{chatId}/messages/{messageId} {
      allow read: if request.auth.uid in get(/databases/$(database)/documents/chats/$(chatId)).data.participants;
      allow create: if request.auth != null;
    }
    match /emergency/{alertId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### Step 8 — Build & Run
1. Connect an Android device (API 24+) or start an emulator
2. Click **Run** (Shift+F10)

---

## 🎨 Color Palette
| Color | Hex | Usage |
|---|---|---|
| Primary Teal | `#00897B` | Brand, buttons, header |
| Primary Dark | `#00695C` | Status bar, dark areas |
| Accent Orange | `#FF5722` | Highlights |
| SOS Red | `#D32F2F` | Emergency button |
| Success Green | `#4CAF50` | Free/donate badge |
| Warning Orange | `#FF9800` | Expiry dates |

---

## 📦 Next Modules to Add
To extend this project further, implement:
1. **Request Medicine** — post requirements, others respond
2. **Admin Panel** — manage listings, ban users
3. **AI Medicine Suggestion** — basic expiry & alternative logic
4. **Pagination** — Firestore cursor-based pagination
5. **Offline Cache** — Room DB for viewed medicines
6. **FCM Notifications** — on new message / SOS

---

## ⚠️ Important Notes
- Replace ALL placeholder values before running
- `google-services.json` is NOT included (personal to your Firebase project)
- Maps API key must be restricted to your app's SHA-1 fingerprint for production
- Test mode Firestore rules expire after 30 days — update before submission
