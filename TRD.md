---

# **TECHNICAL REQUIREMENTS DOCUMENT (TRD)**
## **Campus Canteen Order App**

**Version:** 1.0  
**Date:** February 28, 2026  
**Prepared for:** College Startup Team

---

## **1. SYSTEM ARCHITECTURE OVERVIEW**

### **1.1 High-Level Architecture**

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Mobile App    │    │   Backend APIs   │    │   Payment       │
│   (React Native)│◄──►│   (Firebase)     │◄──►│   Gateway       │
│                 │    │                  │    │   (Razorpay)    │
└─────────────────┘    └──────────────────┘    └─────────────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Push          │    │   Database       │    │   UPI Apps      │
│   Notifications │    │   (Firestore)    │    │   (GPay, etc.)  │
│   (FCM)         │    │                  │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### **1.2 Technology Stack**

**Frontend (Mobile App):**
- **Framework:** Expo React Native
- **Navigation:** React Navigation v7
- **State Management:** React Context API
- **UI Components:** React Native Elements
- **Local Storage:** AsyncStorage

**Backend:**
- **Platform:** Firebase (Free Tier)
- **Authentication:** Firebase Authentication
- **Database:** Cloud Firestore
- **Hosting:** Firebase Hosting
- **Functions:** Cloud Functions (Node.js)
- **Notifications:** Firebase Cloud Messaging

**Payments:**
- **Gateway:** Razorpay Standard
- **Method:** UPI Integration
- **Payouts:** Automatic split payments

**Development Tools:**
- **Version Control:** Git + GitHub
- **Project Management:** Trello (Free)
- **Testing:** Jest + React Native Testing Library
- **Monitoring:** Firebase Crashlytics

---

## **2. API SPECIFICATIONS**

### **2.1 Authentication APIs**

**POST /api/auth/register**
```
Request:
{
  "rollNumber": "CS2023001",
  "password": "student123",
  "name": "Rohan Sharma",
  "email": "rohan@college.edu",
  "phone": "9876543210"
}

Response (Success):
{
  "success": true,
  "userId": "user_12345",
  "message": "Registration successful"
}

Response (Error):
{
  "success": false,
  "error": "Roll number already exists"
}
```

**POST /api/auth/login**
```
Request:
{
  "rollNumber": "CS2023001",
  "password": "student123"
}

Response (Success):
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "user_12345",
    "name": "Rohan Sharma",
    "rollNumber": "CS2023001",
    "role": "student"
  }
}
```

**POST /api/auth/canteen-login**
```
Request:
{
  "canteenId": "canteen_001",
  "password": "canteen123"
}

Response (Success):
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "canteen": {
    "id": "canteen_001",
    "name": "Main Canteen",
    "role": "canteen"
  }
}
```

---

### **2.2 Menu Management APIs**

**GET /api/menu/:canteenId**
```
Response:
{
  "canteenId": "canteen_001",
  "menu": [
    {
      "id": "item_001",
      "name": "Masala Dosa",
      "price": 60,
      "description": "Crispy golden dosa...",
      "category": "South Indian",
      "available": true,
      "image": "https://...",
      "rating": 4.8
    }
  ],
  "lastUpdated": "2026-02-28T10:30:00Z"
}
```

**PUT /api/menu/:canteenId/items/:itemId**
```
Request:
{
  "available": false,
  "note": "Ingredients not available"
}

Response:
{
  "success": true,
  "message": "Menu item updated successfully"
}
```

---

### **2.3 Order Management APIs**

**POST /api/orders**
```
Request:
{
  "userId": "user_12345",
  "canteenId": "canteen_001",
  "items": [
    {
      "itemId": "item_001",
      "quantity": 2,
      "notes": "Less spicy"
    }
  ],
  "paymentMethod": "upi"
}

Response:
{
  "success": true,
  "orderId": "order_12345",
  "orderNumber": "ORD-20260228-001",
  "totalAmount": 120,
  "platformFee": 2,
  "paymentUrl": "https://rzp.io/i/abc123",
  "queuePosition": 3,
  "estimatedTime": 15
}
```

**GET /api/orders/:orderId**
```
Response:
{
  "orderId": "order_12345",
  "orderNumber": "ORD-20260228-001",
  "status": "preparing", // received, preparing, ready, completed
  "items": [...],
  "totalAmount": 120,
  "platformFee": 2,
  "queuePosition": 2,
  "estimatedTime": 10,
  "createdAt": "2026-02-28T10:30:00Z",
  "preparedAt": null,
  "completedAt": null
}
```

**PUT /api/orders/:orderId/status**
```
Request:
{
  "status": "ready",
  "preparedAt": "2026-02-28T10:45:00Z"
}

Response:
{
  "success": true,
  "message": "Order status updated"
}
```

---

### **2.4 Payment APIs**

**POST /api/payments/create**
```
Request:
{
  "orderId": "order_12345",
  "amount": 122, // total + platform fee
  "currency": "INR",
  "description": "Canteen order payment"
}

Response:
{
  "success": true,
  "paymentId": "pay_12345",
  "paymentUrl": "https://api.razorpay.com/v1/payments/...",
  "upiIntentUrl": "upi://pay?pa=merchant@upi&pn=CampusCanteen&am=122&tn=Order%20ORD-001"
}
```

**POST /api/payments/webhook**
```
Request (from Razorpay):
{
  "event": "payment.captured",
  "payload": {
    "payment": {
      "entity": {
        "id": "pay_12345",
        "amount": 12200, // in paise
        "currency": "INR",
        "status": "captured",
        "order_id": "order_12345"
      }
    }
  }
}

Response:
{
  "success": true,
  "message": "Payment processed successfully"
}
```

---

## **3. DATABASE SCHEMA**

### **3.1 Firestore Collections Structure**

**Users Collection**
```
/users/{userId}
├── userId: string
├── rollNumber: string
├── name: string
├── email: string
├── phone: string
├── role: "student" | "canteen"
├── createdAt: timestamp
├── lastLogin: timestamp
└── isActive: boolean
```

**Canteens Collection**
```
/canteens/{canteenId}
├── canteenId: string
├── name: string
├── location: string
├── contactPerson: string
├── contactPhone: string
├── isActive: boolean
├── createdAt: timestamp
└── settings: {
    maxOrdersPerHour: number,
    preparationTime: number // in minutes
}
```

**Menu Items Collection**
```
/canteens/{canteenId}/menu/{itemId}
├── itemId: string
├── name: string
├── description: string
├── price: number
├── category: string
├── available: boolean
├── image: string
├── rating: number
├── createdAt: timestamp
└── updatedAt: timestamp
```

**Orders Collection**
```
/orders/{orderId}
├── orderId: string
├── orderNumber: string
├── userId: string
├── canteenId: string
├── items: array[{
    itemId: string,
    name: string,
    price: number,
    quantity: number,
    notes: string
}]
├── totalAmount: number
├── platformFee: number
├── paymentStatus: "pending" | "paid" | "failed"
├── orderStatus: "received" | "preparing" | "ready" | "completed"
├── queuePosition: number
├── estimatedTime: number // in minutes
├── createdAt: timestamp
├── preparedAt: timestamp
├── completedAt: timestamp
├── pickupCode: string
└── paymentId: string
```

**Payments Collection**
```
/payments/{paymentId}
├── paymentId: string
├── orderId: string
├── userId: string
├── amount: number
├── currency: string
├── status: "created" | "captured" | "failed" | "refunded"
├── paymentMethod: "upi"
├── razorpayPaymentId: string
├── createdAt: timestamp
└── updatedAt: timestamp
```

---

## **4. SECURITY REQUIREMENTS**

### **4.1 Authentication & Authorization**

**JWT Token Security:**
- Token expiration: 24 hours
- Refresh token mechanism
- Secure token storage (AsyncStorage with encryption)
- Role-based access control (student vs canteen)

**Password Security:**
- Minimum 6 characters
- Hashing with bcrypt (handled by Firebase Auth)
- No password storage in client app

**API Security:**
- All API calls require authentication token
- Rate limiting: 100 requests/minute per user
- Input validation and sanitization
- HTTPS enforcement

### **4.2 Data Protection**

**User Data:**
- Personal data encryption at rest
- Minimal data collection (only essential fields)
- Data retention policy: 1 year after last activity
- GDPR-style privacy controls

**Payment Data:**
- No sensitive payment data stored
- PCI DSS compliance through Razorpay
- Tokenization for payment methods
- Secure payment flow redirection

### **4.3 App Security**

**Mobile App Security:**
- Certificate pinning for API calls
- Code obfuscation for production builds
- Secure storage for sensitive data
- Jailbreak/root detection (optional)

**Firebase Security Rules:**
```javascript
// Example security rules
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    
    match /orders/{orderId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null && request.auth.token.role == 'student';
      allow update: if request.auth != null && request.auth.token.role == 'canteen';
    }
  }
}
```

---

## **5. PERFORMANCE REQUIREMENTS**

### **5.1 Response Time Targets**

**API Response Times:**
- Authentication: < 500ms
- Menu retrieval: < 1 second
- Order placement: < 2 seconds
- Order status updates: < 500ms
- Payment processing: < 5 seconds

**App Performance:**
- App launch time: < 3 seconds
- Screen transitions: < 500ms
- Image loading: < 2 seconds (with caching)
- Search results: < 1 second

### **5.2 Scalability Requirements**

**Concurrent Users:**
- Target: 500 concurrent users
- Peak load: 1000 concurrent users
- Database connections: Auto-scaling with Firestore

**Data Storage:**
- Initial capacity: 1GB (Firebase free tier)
- Growth rate: 100MB/month
- Backup: Automatic daily backups

**Network Optimization:**
- Image compression and caching
- API response compression
- Offline functionality for order placement
- Background sync for data updates

---

## **6. INTEGRATION REQUIREMENTS**

### **6.1 Payment Gateway Integration**

**Razorpay Integration:**
- SDK integration for React Native
- UPI intent handling
- Webhook setup for payment notifications
- Split payment configuration
- Test mode for development

**UPI App Integration:**
- Deep linking to UPI apps
- QR code generation for payments
- Payment status verification
- Fallback payment methods

### **6.2 Notification Integration**

**Firebase Cloud Messaging:**
- Push notification setup
- Topic-based notifications
- Rich notifications with images
- Notification scheduling for order reminders

**In-App Notifications:**
- Real-time order status updates
- Order ready notifications
- Payment confirmation alerts
- System maintenance notifications

### **6.3 Third-Party Integrations**

**Map Integration (Future):**
- Google Maps API for canteen locations
- Distance calculation
- Navigation integration

**Analytics Integration:**
- Firebase Analytics for usage tracking
- Crash reporting with Crashlytics
- Performance monitoring
- User behavior analytics

---

## **7. TESTING REQUIREMENTS**

### **7.1 Unit Testing**

**Frontend Testing:**
- Component rendering tests
- User interaction tests
- State management tests
- API integration tests

**Backend Testing:**
- Cloud Function tests
- Database operation tests
- Security rule validation
- API endpoint tests

### **7.2 Integration Testing**

**End-to-End Testing:**
- Complete order flow testing
- Payment processing testing
- Notification delivery testing
- Cross-platform compatibility testing

**Performance Testing:**
- Load testing for 500+ users
- Stress testing for peak hours
- Database performance testing
- API response time validation

### **7.3 User Acceptance Testing**

**Beta Testing:**
- Internal team testing
- Student group testing
- Canteen staff testing
- Feedback collection and iteration

---

## **8. DEPLOYMENT REQUIREMENTS**

### **8.1 Development Environment**

**Local Development:**
- Expo CLI for mobile app development
- Firebase Emulator Suite for backend
- Local database for testing
- Mock payment gateway for development

**Version Control:**
- Git workflow with feature branches
- GitHub for code hosting
- Pull request review process
- Automated testing on commits

### **8.2 Production Deployment**

**Mobile App Deployment:**
- Expo build for iOS and Android
- App Store submission process
- Google Play Store submission
- Beta testing with TestFlight and Play Console

**Backend Deployment:**
- Firebase project setup
- Production database configuration
- Security rules deployment
- Cloud Function deployment

### **8.3 Monitoring & Maintenance**

**Application Monitoring:**
- Firebase Crashlytics for error tracking
- Performance monitoring
- User analytics
- Payment success rate monitoring

**Database Monitoring:**
- Firestore usage tracking
- Query performance monitoring
- Storage usage alerts
- Backup verification

---
