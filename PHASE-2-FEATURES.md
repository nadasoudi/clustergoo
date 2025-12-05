# **ClusterGo – Phase 2 Features & Implementation**

This document summarizes the development and technical implementation of **Phase 2** of the ClusterGo project. This phase transforms ClusterGo from a UI-only prototype into a fully functional ride-sharing system built on **Firebase Realtime Database**, **REST APIs**, **Provider state management**, and **Firebase Cloud Messaging**.

---

## **Table of Contents**

* Overview
* Features Implemented
* System Architecture
* Firebase Realtime Database Integration
* Profile API Integration
* Push Notifications
* Project Structure
* Dependencies
* Setup Instructions
* Usage Guide
* API Documentation
* Firebase Database Structure
* State Management
* Error Handling
* Future Enhancements
* License

---

## **1. Overview**

Phase 2 focuses on connecting ClusterGo to real backend services and enabling dynamic ride-sharing functionality. The system now supports:

* Real-time ride creation and synchronization
* REST-based profile data retrieval
* Application-wide state management via Provider
* Push notifications for new rides and updates
* Error handling and reliable loading states

These updates move the app significantly closer to a production-ready platform.

### **Main Objectives**

* Enable real-time communication using Firebase Realtime Database
* Integrate profile data through a REST API
* Implement predictable state management
* Add push notifications for all major events
* Improve reliability and error recovery

---

## **2. Features Implemented**

### **2.1 Firebase Realtime Database Integration**

**Purpose:** Maintain synchronized ride data for all users in real time.

**Implemented Functionality:**

* Create ride intents
* Listen to all rides with real-time updates
* Update or delete ride entries
* Track and modify available seats

**User Benefits:**

* Newly created rides appear instantly
* Updates require no manual refresh
* Seat counts are always correct and up to date

---

### **2.2 Profile Screen with API Integration**

**Purpose:** Fetch and display user data from a remote REST API.

**Features:**

* Retrieve profile details via HTTP GET
* Display name, email, university, and member history
* Show total rides and user preferences
* Auto-generate avatar initials
* Format dates and statistics for readability

---

### **2.3 Push Notifications**

**Purpose:** Alert users when new rides are created or updated.

**Features:**

* Firebase Cloud Messaging setup
* Local display with `flutter_local_notifications`
* Foreground and background notification handling
* Automatic FCM token generation

---

### **2.4 State Management Using Provider**

**Purpose:** Maintain a clear separation between UI and application logic.

**Providers Used:**

* `RidesProvider`
* `ProfileProvider`

**Benefits:**

* Predictable and centralized state updates
* Lightweight UI rebuilds
* Cleaner, testable logic layers

---

## **3. System Architecture**

Architecture Pattern: **Clean Architecture + MVVM**

### **Directory Layout**

```
lib/
├── models/
├── services/
├── providers/
├── screens/
├── widgets/
└── utils/
```

### **Data Flow**

```
UI Layer
   ↓
Provider (State Layer)
   ↓
Service Layer
   ↓
Firebase / REST API
```

**Design Principles Used:**

* Single Responsibility
* Separation of Concerns
* Reusability
* Dependency Injection

---

## **4. Firebase Realtime Database Integration**

### **4.1 Firebase Manager**

File: `lib/services/firebase_manager.dart`
Pattern: **Singleton**

**Key Methods:**

Create a ride:

```dart
Future<String> createRide(RideIntent ride)
```

Stream rides in real time:

```dart
Stream<List<RideIntent>> getRidesStream()
```

Update a ride:

```dart
Future<void> updateRide(String rideId, Map<String, dynamic> updates)
```

Delete a ride:

```dart
Future<void> deleteRide(String rideId)
```

Modify available seats:

```dart
Future<void> updateAvailableSeats(String rideId, int newSeats)
```

---

### **4.2 RideIntent Model**

File: `lib/models/ride_intent.dart`

Fields include:

* id
* userName
* pickup
* destination
* time
* availableSeats

Supports:

* `toMap()`
* `fromMap()`

---

### **4.3 Rides Provider**

File: `lib/providers/rides_provider.dart`

**Responsibilities:**

* Maintain the rides list
* Manage loading & error states
* Handle CRUD operations
* Subscribe to Firebase streams

Key Properties:

```dart
List<RideIntent> rides
bool isLoading
String? error
```

---

### **4.4 Home Screen**

File: `lib/screens/home_screen.dart`

**Features:**

* Real-time ride display
* Error and empty state handling
* Pull-to-refresh
* Loading indicators

The UI cycles through:

1. Loading
2. Error
3. Empty
4. Populated data

---

### **4.5 Create Ride Screen**

File: `lib/screens/create_ride_screen.dart`

**Features:**

* Full form validation
* Pickup & destination fields
* Date/time pickers
* Seats slider (1–4)
* Loading feedback
* Clear success/error messages

---

## **5. Profile API Integration**

### **5.1 API Service**

File: `lib/services/api_service.dart`
Endpoint:

```
GET https://mocki.io/v1/968771e1-87c6-40a5-83c8-77d2b5aa040a
```

**Features:**

* 10-second timeout
* Network and server error handling
* Status code validation

---

### **5.2 UserProfile Model**

File: `lib/models/user_profile.dart`

Contains:

* name
* email
* university
* memberSince
* totalRides
* preferences

Computed values:

* `initials`
* `formattedMemberSince`
* `formattedTotalRides`

---

### **5.3 Preference Model**

File: `lib/models/preference.dart`

Fields:

* type
* allowed

---

### **5.4 Profile Provider**

File: `lib/providers/profile_provider.dart`

Handles:

* Fetching profile data
* Loading/error states
* Retry logic

---

### **5.5 Profile Screen**

File: `lib/screens/profile_screen.dart`

Features:

* Auto-fetch on screen load
* Retry on failure
* Manual refresh
* Dynamic preferences display

---

## **6. Push Notifications System**

File: `lib/services/notification_manager.dart`

Features include:

* Permission requests
* Foreground & background notifications
* FCM token retrieval
* Android channel configuration
* iOS Darwin setup

---

## **7. Project Structure**

```
lib/
├── main.dart
├── firebase_options.dart
├── models/
│   ├── ride_intent.dart
│   ├── user_profile.dart
│   └── preference.dart
├── services/
│   ├── firebase_manager.dart
│   ├── api_service.dart
│   └── notification_manager.dart
├── providers/
│   ├── rides_provider.dart
│   └── profile_provider.dart
├── screens/
│   ├── home_screen.dart
│   ├── create_ride_screen.dart
│   └── profile_screen.dart
├── widgets/
│   └── ride_card.dart
└── utils/
    ├── colors.dart
    └── sample_data.dart
```

---

## **8. Dependencies**

```yaml
firebase_core: ^4.2.1
firebase_database: ^12.1.0
firebase_messaging: ^16.0.4
provider: ^6.1.2
flutter_local_notifications: ^19.5.0
http: ^1.2.0
intl: ^0.19.0
logger: ^2.6.2
```

---

## **9. Setup Instructions**

### Clone

```bash
git clone https://github.com/nadasoudi/clustergo.git
cd clustergo
```

### Install Packages

```bash
flutter pub get
```

### Firebase Setup

* Add `google-services.json` to `android/app/`
* Add `GoogleService-Info.plist` to `ios/Runner/`
* Enable Realtime Database and Cloud Messaging

### Run

```bash
flutter run
```

---

## **10. Usage Guide**

### Create a Ride

* Go to **Create Ride**
* Fill the form
* Submit

### View Available Rides

* Open **Home**
* Rides update automatically

### View Profile

* Open **Profile**
* Tap refresh if needed

---

## **11. API Documentation**

Example Response:

```json
{
  "name": "Rghda Salah",
  "email": "rghda.shehatah@gmail.com",
  "university": "Zewail City",
  "member_since": "2003-9-8",
  "total_rides": 50,
  "preferences": [
    { "type": "music", "allowed": false },
    { "type": "talking", "allowed": false },
    { "type": "ac", "allowed": true }
  ]
}
```

---

## **12. Firebase Database Structure**

```
/rides
  /{rideId}
    id
    userName
    pickup
    destination
    time
    availableSeats
```

---

## **13. State Management**

Pattern: **Provider (MVVM)**

### State Flow

```
User → Screen → Provider → Service → Data → notifyListeners → UI
```

**Best Practices:**

* Use `listen: false` for actions
* Use `Consumer` to limit rebuilds
* Clean up stream subscriptions

---

## **14. Error Handling**

Supports:

* Network issues
* Firebase permission failures
* API errors
* Invalid inputs

Includes:

* Retry options
* User-friendly messages
* Logging via `logger`

---

## **15. Future Enhancements**

* Authentication
* Ride booking
* In-app chat
* Maps & routing
* Payments
* Admin dashboard
* Offline mode
* Analytics

---

## **16. License**

ClusterGo – University Ride-Sharing Application
Phase 2 — December 2025
Version 1.0

