# 🌍 CollaboCal - Community Action Planner

CollaboCal is a hyper-local, collaborative platform for organizing community-driven events and activities. Users can create "Action Cards" for neighborhood events, discover nearby activities, and collaborate in real-time using shared checklists and chat.

## 📋 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Backend Setup](#backend-setup)
  - [Frontend Setup](#frontend-setup)
- [Running the Application](#-running-the-application)
- [Feature Details](#-feature-details)
- [Architecture](#-architecture)
- [Security](#-security)
- [Remaining Tasks](#-remaining-tasks)
- [Contributing](#-contributing)

---

## ✨ Features

### 🔐 Authentication & User Management
- **Google OAuth Sign-In**: One-click authentication using Google accounts
- **Email/Password Authentication**: Traditional sign-up and login flow
- **User Profiles**: Automatic profile creation in Firestore with display name, email, and avatar
- **Protected Routes**: Secure access to authenticated-only features

**Implementation**: Firebase Authentication SDK integrated in [`AuthContext.js`](collabocal-frontend/src/contexts/AuthContext.js) provides authentication state management across the React application. Backend middleware in [`middleware.py`](collabocal-backend/app/middleware.py) verifies ID tokens using Firebase Admin SDK.

### 📍 Location-Based Action Discovery
- **Interactive Map View**: Browse community actions on an interactive Leaflet map
- **Geospatial Queries**: Efficient nearby action discovery using geohashing
- **Custom Markers**: Visual distinction between action types
- **Marker Clustering**: Automatic grouping of nearby markers for better map readability
- **Location Search**: Find actions by address or place name
- **Radius Filtering**: Adjustable search radius (1-50km)

**Implementation**: 
- Geohashing service in [`geohash_service.py`](collabocal-backend/app/services/geohash_service.py) encodes locations into geohashes for efficient proximity queries
- Advanced map component in [`AdvancedMapView.js`](collabocal-frontend/src/components/map/AdvancedMapView.js) uses React-Leaflet with marker clustering
- Geolocation hook in [`useGeolocation.js`](collabocal-frontend/src/hooks/useGeolocation.js) handles browser geolocation API

### 🎯 Action Management
- **Create Actions**: Post new community events with title, description, location, and date
- **View Actions**: Browse all actions in list or map view
- **Edit/Delete Actions**: Creators can modify or remove their actions
- **Join Actions**: Participate in community events with one click
- **Action Dashboard**: Dedicated collaboration space for each action

**Implementation**: RESTful API endpoints in [`actions.py`](collabocal-backend/app/routes/actions.py) handle CRUD operations. Frontend components in [`ActionCard.js`](collabocal-frontend/src/components/action/ActionCard.js) and [`ActionDashboard.js`](collabocal-frontend/src/components/dashboard/ActionDashboard.js) provide the UI.

### 🤝 Real-Time Collaboration
- **Shared Checklist**: Collaborative task list with real-time updates
  - Add/remove checklist items
  - Toggle completion status
  - See who completed each task
  - Automatic synchronization across all participants
  
- **Group Chat**: Real-time messaging within action groups
  - Instant message delivery
  - User avatars and names
  - Timestamp display
  - Auto-scroll to latest messages
  
- **Participant Management**: View all joined members with avatars

**Implementation**: 
- Direct Firestore listeners in [`useFirestore.js`](collabocal-frontend/src/hooks/useFirestore.js) hook provide real-time updates
- Subcollections in Firestore (`checklistItems/` and `chatMessages/`) enable granular real-time synchronization
- Components in [`SharedChecklist.js`](collabocal-frontend/src/components/dashboard/SharedChecklist.js) and [`ChatWindow.js`](collabocal-frontend/src/components/dashboard/ChatWindow.js) manage real-time UI updates

### ⚙️ Settings & Preferences
- **Location Preferences**: Save default location and search radius
- **Permission Handling**: Graceful handling of location access permissions
- **Default Map Center**: Automatically center map on user's preferred location

**Implementation**: Location settings component in [`LocationSettings.js`](collabocal-frontend/src/components/settings/LocationSettings.js) manages user preferences stored in Firestore.

---

## 🛠️ Tech Stack

### Frontend
- **React 19.2.0**: Modern UI with functional components and hooks
- **React Router 6.20.1**: Client-side routing
- **Leaflet 1.9.4**: Interactive maps
- **React-Leaflet 4.2.1**: React bindings for Leaflet
- **React-Leaflet-Cluster 2.1.0**: Marker clustering
- **Firebase 10.7.1**: Authentication and Firestore database
- **Axios 1.6.2**: HTTP client for API calls

### Backend
- **Flask 3.0.0**: Python web framework
- **Flask-CORS 4.0.0**: Cross-origin resource sharing
- **Flask-Limiter 3.5.0**: API rate limiting
- **Flask-Caching 2.1.0**: Response caching
- **Firebase Admin 6.3.0**: Server-side Firebase SDK
- **PyGeohash 1.2.0**: Geospatial indexing
- **Python-dotenv 1.0.0**: Environment variable management

### Database & Services
- **Firestore**: NoSQL document database for real-time data
- **Firebase Authentication**: User authentication service
- **Firebase Admin SDK**: Server-side Firebase operations

---

## 📁 Project Structure

```
Collab/
├── collabocal-backend/          # Flask API backend
│   ├── app/
│   │   ├── __init__.py          # Flask app factory
│   │   ├── config.py            # Configuration settings
│   │   ├── middleware.py        # Authentication middleware
│   │   ├── routes/
│   │   │   ├── actions.py       # Action CRUD endpoints
│   │   │   └── users.py         # User management endpoints
│   │   ├── services/
│   │   │   ├── firebase_service.py  # Firebase Admin operations
│   │   │   └── geohash_service.py   # Geospatial queries
│   │   └── utils/               # Helper utilities
│   ├── .env.example             # Environment template
│   ├── requirements.txt         # Python dependencies
│   └── run.py                   # Application entry point
│
├── collabocal-frontend/         # React frontend
│   ├── src/
│   │   ├── components/
│   │   │   ├── action/          # Action card components
│   │   │   ├── dashboard/       # Collaboration components
│   │   │   ├── map/             # Map view components
│   │   │   ├── settings/        # Settings components
│   │   │   └── common/          # Shared components
│   │   ├── pages/               # Route pages
│   │   ├── contexts/            # React contexts (Auth)
│   │   ├── firebase/            # Firebase config & services
│   │   ├── hooks/               # Custom React hooks
│   │   ├── services/            # API service layer
│   │   └── utils/               # Helper functions
│   ├── .env.example             # Environment template
│   └── package.json             # Node dependencies
│
└── README.md                     # This file
```

---

## 🚀 Getting Started

### Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v18 or higher) and npm
- **Python** (v3.9 or higher) and pip
- **Firebase Project** with:
  - Authentication enabled (Google OAuth and Email/Password)
  - Firestore database created
  - Service account key downloaded

### Backend Setup

1. **Navigate to backend directory**
   ```bash
   cd collabocal-backend
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   ```

3. **Activate virtual environment**
   - Windows:
     ```bash
     venv\Scripts\activate
     ```
   - macOS/Linux:
     ```bash
     source venv/bin/activate
     ```

4. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

5. **Configure environment variables**
   ```bash
   cp .env.example .env
   ```
   
   Edit `.env` and add your configuration:
   ```env
   FLASK_ENV=development
   FLASK_DEBUG=True
   FIREBASE_CREDENTIALS_PATH=path/to/your/serviceAccountKey.json
   ALLOWED_ORIGINS=http://localhost:3000
   ```

6. **Add Firebase Service Account Key**
   - Go to Firebase Console → Project Settings → Service Accounts
   - Click "Generate New Private Key"
   - Save the JSON file in a secure location
   - Update `FIREBASE_CREDENTIALS_PATH` in `.env` to point to this file

### Frontend Setup

1. **Navigate to frontend directory**
   ```bash
   cd collabocal-frontend
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Configure environment variables**
   ```bash
   cp .env.example .env
   ```
   
   Edit `.env` and add your Firebase configuration:
   ```env
   REACT_APP_API_URL=http://localhost:5000/api
   REACT_APP_FIREBASE_API_KEY=your_api_key
   REACT_APP_FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
   REACT_APP_FIREBASE_PROJECT_ID=your_project_id
   REACT_APP_FIREBASE_STORAGE_BUCKET=your_project.appspot.com
   REACT_APP_FIREBASE_MESSAGING_SENDER_ID=your_sender_id
   REACT_APP_FIREBASE_APP_ID=your_app_id
   ```

   Find these values in Firebase Console → Project Settings → General → Your apps → Web app

---

## ▶️ Running the Application

### Start Backend Server

```bash
cd collabocal-backend
# Make sure virtual environment is activated
python run.py
```

The backend will start on `http://localhost:5000`

### Start Frontend Development Server

```bash
cd collabocal-frontend
npm start
```

The frontend will open automatically at `http://localhost:3000`

### Access the Application

1. Open your browser to `http://localhost:3000`
2. Sign in with Google or create an account with email/password
3. Allow location permissions when prompted (optional but recommended)
4. Start creating or joining community actions!

---

## 🔍 Feature Details

### How Authentication Works

1. User clicks "Sign In with Google" or enters email/password
2. Firebase Authentication SDK handles the authentication flow
3. Upon success, Firebase returns an ID token and user object
4. User data is stored in [`AuthContext`](collabocal-frontend/src/contexts/AuthContext.js)
5. ID token is included in all API requests via Authorization header
6. Backend [`middleware.py`](collabocal-backend/app/middleware.py) verifies token before processing requests

### How Location-Based Discovery Works

1. **Geohash Encoding**: When creating an action, the location coordinates are encoded into a geohash string (precision 5) in [`geohash_service.py`](collabocal-backend/app/services/geohash_service.py)
2. **Storage**: Geohash is stored alongside the action in Firestore
3. **Querying**: When searching for nearby actions:
   - User's current location is encoded to a geohash
   - Geohash neighbors are calculated to cover the search radius
   - Firestore query filters actions by geohash prefix for efficient lookups
4. **Distance Filtering**: Results are further filtered by exact distance calculation
5. **Map Display**: Actions are rendered as markers on the Leaflet map in [`AdvancedMapView.js`](collabocal-frontend/src/components/map/AdvancedMapView.js)

### How Real-Time Collaboration Works

1. **Firestore Listeners**: When a user opens an action dashboard, React hooks subscribe to Firestore subcollections
2. **Chat Messages**: 
   - Messages are stored in `actions/{actionId}/chatMessages/` subcollection
   - [`useFirestore.js`](collabocal-frontend/src/hooks/useFirestore.js) hook creates a real-time listener using `onSnapshot()`
   - New messages trigger immediate UI updates in [`ChatWindow.js`](collabocal-frontend/src/components/dashboard/ChatWindow.js)
3. **Checklist Items**:
   - Items stored in `actions/{actionId}/checklistItems/` subcollection
   - Real-time sync via Firestore listeners
   - Optimistic UI updates for instant feedback
   - Displayed in [`SharedChecklist.js`](collabocal-frontend/src/components/dashboard/SharedChecklist.js)
4. **Participants**:
   - Join/leave operations update `actions/{actionId}/participants/` subcollection
   - Participant list updates automatically via listeners
   - Shown in [`ParticipantList.js`](collabocal-frontend/src/components/dashboard/ParticipantList.js)

### How API Requests Work

1. Frontend API service ([`api.js`](collabocal-frontend/src/services/api.js)) uses Axios with interceptors
2. Interceptor automatically adds Firebase ID token to Authorization header
3. Backend receives request and extracts token
4. [`middleware.py`](collabocal-backend/app/middleware.py) verifies token using Firebase Admin SDK
5. If valid, request proceeds with `request.user_id` containing the authenticated user's UID
6. Response is returned to frontend with appropriate status code

---

## 🏗️ Architecture

### Data Flow

```
User → React App → Firebase Auth (Get Token)
                 ↓
React App → Flask API (with Token) → Firebase Admin (Verify Token)
                 ↓
Flask API → Firestore (Write Data)
                 ↓
Firestore → React App (Real-time Listeners) → UI Update
```

### Database Schema

**Users Collection**: `users/{userId}`
- `uid`: User ID
- `displayName`: User's display name
- `email`: User's email
- `photoURL`: Profile picture URL
- `lastKnownLocation`: GeoPoint of last known location

**Actions Collection**: `actions/{actionId}`
- `title`: Action title
- `description`: Detailed description
- `createdAt`: Creation timestamp
- `eventDate`: Scheduled event date
- `creatorUid`: Creator's user ID
- `locationName`: Human-readable location
- `location`: GeoPoint (latitude, longitude)
- `geohash`: Encoded geohash for spatial queries

**Subcollections**:
- `participants/{userId}`: Action participants
- `checklistItems/{itemId}`: Shared checklist items
- `chatMessages/{messageId}`: Group chat messages

---

## 🔒 Security

### Implemented Security Measures

1. **Firebase Authentication**: Industry-standard OAuth and token-based auth
2. **Token Verification**: All protected endpoints verify Firebase ID tokens
3. **Firestore Security Rules**: Database-level access control
4. **CORS Configuration**: Restricted to allowed origins in [`config.py`](collabocal-backend/app/config.py)
5. **Rate Limiting**: API rate limits configured in Flask-Limiter
6. **Input Validation**: Server-side validation of all user inputs
7. **Environment Variables**: Sensitive credentials stored in `.env` files

### Firestore Security Rules (to be configured)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read: if request.auth != null;
      allow write: if request.auth.uid == userId;
    }
    
    match /actions/{actionId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null;
      allow update: if request.auth.uid == resource.data.creatorUid;
      allow delete: if request.auth.uid == resource.data.creatorUid;
      
      match /participants/{userId} {
        allow read: if request.auth != null;
        allow write: if request.auth.uid == userId;
      }
      
      match /checklistItems/{itemId} {
        allow read: if request.auth != null;
        allow write: if request.auth != null && 
                       exists(/databases/$(database)/documents/actions/$(actionId)/participants/$(request.auth.uid));
      }
      
      match /chatMessages/{messageId} {
        allow read: if request.auth != null;
        allow create: if request.auth != null && 
                        exists(/databases/$(database)/documents/actions/$(actionId)/participants/$(request.auth.uid));
      }
    }
  }
}
```

---

## 📝 Remaining Tasks

### Required Setup Steps (To be done by you)

1. **Firebase Project Configuration**
   - [ ] Create a Firebase project at https://console.firebase.google.com
   - [ ] Enable Authentication providers:
     - [ ] Google OAuth
     - [ ] Email/Password
   - [ ] Create a Firestore database
   - [ ] Generate and download service account key
   - [ ] Add Firebase web app configuration to frontend `.env`

2. **Firestore Security Rules**
   - [ ] Copy the security rules from the [Security](#security) section above
   - [ ] Navigate to Firebase Console → Firestore Database → Rules
   - [ ] Paste and publish the security rules

3. **Environment Configuration**
   - [ ] Create backend `.env` file with Firebase credentials path
   - [ ] Create frontend `.env` file with Firebase web config
   - [ ] Update `ALLOWED_ORIGINS` for production deployment

4. **Optional Enhancements**
   - [ ] Add Firestore indexes for complex queries (Firebase will prompt if needed)
   - [ ] Configure Firebase Storage for user-uploaded images
   - [ ] Set up Firebase Hosting for production deployment
   - [ ] Configure custom domain (if applicable)

### Potential Future Enhancements

- **Notifications**: Push notifications for new messages and action updates
- **Image Uploads**: Allow users to add photos to actions and chat
- **Action Categories**: Categorize actions (cleanup, sports, social, etc.)
- **Recurring Events**: Support for weekly/monthly recurring actions
- **Action Ratings**: Rate and review completed actions
- **Advanced Filters**: Filter by date, category, distance, popularity
- **Social Sharing**: Share actions on social media
- **Mobile App**: React Native mobile application
- **Analytics Dashboard**: Track action participation and engagement metrics

---

## 🤝 Contributing

This is a portfolio project demonstrating full-stack development capabilities. If you'd like to contribute or have suggestions:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is created as a portfolio demonstration and is available for educational purposes.

---

## 👨‍💻 Developer

Built with ❤️ as a demonstration of modern full-stack development skills, featuring:
- React with hooks and context
- Flask RESTful API
- Firebase integration
- Real-time collaboration
- Geospatial queries
- Responsive design

---

## 📚 Additional Documentation

- [`COLLABOCAL_ARCHITECTURE.md`](COLLABOCAL_ARCHITECTURE.md) - Detailed system architecture
- [`IMPLEMENTATION_GUIDE.md`](IMPLEMENTATION_GUIDE.md) - Step-by-step implementation guide
- [`SETUP_PROGRESS.md`](SETUP_PROGRESS.md) - Development progress tracker

---

**Questions or Issues?** Feel free to open an issue in the repository or reach out directly.