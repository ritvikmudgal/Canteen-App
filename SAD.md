---

# **SYSTEM ARCHITECTURE DOCUMENT**
## **Campus Canteen Order App**

**Version:** 1.0  
**Date:** February 28, 2026  
**Prepared for:** College Startup Team

---

## **1. ARCHITECTURAL OVERVIEW**

### **1.1 System Context Diagram**

```
┌─────────────────────────────────────────────────────────────────┐
│                          EXTERNAL USERS                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────┐ │
│  │   STUDENTS      │    │  CANTEEN STAFF  │    │   CANTEEN    │ │
│  │                 │    │                 │    │    OWNERS    │ │
│  │ • Order Food    │    │ • Manage Orders │    │ • Receive    │ │
│  │ • Make Payment  │    │ • Update Menu   │    │   Revenue    │ │
│  │ • Track Orders  │    │ • Mark Ready    │    │ • Analytics  │ │
│  └─────────────────┘    └─────────────────┘    └──────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ Internet/College WiFi
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                        LOAD BALANCER                            │
│                    (Firebase Hosting)                           │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      FIREBASE CLOUD                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────┐ │
│  │   FIREBASE      │    │   CLOUD         │    │   FIREBASE   │ │
│  │   AUTH          │    │   FUNCTIONS     │    │   MESSAGING  │ │
│  │                 │    │                 │    │              │ │
│  │ • User Login    │    │ • Order Logic   │    │ • Push       │ │
│  │ • Role Mgmt     │    │ • Payment Logic │    │   Notifications│ │
│  │ • Security      │    │ • Webhooks      │    │ • Order Alerts │ │
│  └─────────────────┘    └─────────────────┘    └──────────────┘ │
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐                     │
│  │   CLOUD         │    │   FIREBASE      │                     │
│  │   STORAGE       │    │   ANALYTICS     │                     │
│  │                 │    │                 │                     │
│  │ • Menu Images   │    │ • Usage Stats   │                     │
│  │ • Receipts      │    │ • Crash Reports │                     │
│  │ • Logs          │    │ • Performance   │                     │
│  └─────────────────┘    └─────────────────┘                     │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                    CLOUD FIRESTORE                          │ │
│  │                                                             │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────┐ │ │
│  │  │    USERS    │  │   CANTEENS  │  │        ORDERS        │ │ │
│  │  │             │  │             │  │                      │ │ │
│  │  │ • Students  │  │ • Canteen   │  │ • Order Details      │ │ │
│  │  │ • Staff     │  │ • Location  │  │ • Status Tracking    │ │ │
│  │  │ • Profiles  │  │ • Contact   │  │ • Payment Info       │ │ │
│  │  └─────────────┘  └─────────────┘  └──────────────────────┘ │ │
│  │                                                             │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────┐ │ │
│  │  │    MENU     │  │  PAYMENTS   │  │      NOTIFICATIONS   │ │ │
│  │  │             │  │             │  │                      │ │ │
│  │  │ • Items     │  │ • Payment   │  │ • Order Updates      │ │ │
│  │  │ • Prices    │  │   Status    │  │ • Promotions         │ │ │
│  │  │ • Avail.    │  │ • Gateway   │  │ • System Alerts      │ │ │
│  │  └─────────────┘  └─────────────┘  └──────────────────────┘ │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      EXTERNAL SERVICES                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────┐ │
│  │   RAZORPAY      │    │   UPI APPS      │    │   GOOGLE     │ │
│  │   PAYMENT       │    │   (GPay, etc.)  │    │   SERVICES   │ │
│  │   GATEWAY       │    │                 │    │              │ │
│  │ • UPI Payments  │    │ • Payment       │    │ • Maps       │ │
│  │ • Split Payouts │    │   Processing    │    │ • Analytics  │ │
│  │ • Webhooks      │    │ • QR Codes      │    │ • Auth       │ │
│  └─────────────────┘    └─────────────────┘    └──────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

---

## **2. COMPONENT ARCHITECTURE**

### **2.1 Mobile Application Architecture**

```
┌─────────────────────────────────────────────────────────────────┐
│                    REACT NATIVE APP                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────┐ │
│  │   UI LAYER      │    │   BUSINESS      │    │   DATA       │ │
│  │                 │    │   LOGIC LAYER   │    │   LAYER      │ │
│  │ • Screens       │    │                 │    │              │ │
│  │ • Components    │    │ • Auth Service  │    │ • API Calls  │ │
│  │ • Navigation    │    │ • Order Service │    │ • Local      │ │
│  │ • Styling       │    │ • Payment       │    │   Storage    │ │
│  │                 │    │   Service       │    │ • State      │ │
│  │                 │    │ • Notification  │    │   Management │ │
│  │                 │    │   Service       │    │              │ │
│  └─────────────────┘    └─────────────────┘    └──────────────┘ │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                  EXTERNAL INTEGRATIONS                      │ │
│  │                                                             │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────┐ │ │
│  │  │   FIREBASE  │  │   RAZORPAY  │  │   DEVICE             │ │ │
│  │  │   SERVICES  │  │   SDK       │  │   SERVICES           │ │ │
│  │  │             │  │             │  │                      │ │ │
│  │  │ • Auth      │  │ • Payment   │  │ • Notifications      │ │ │
│  │  │ • Firestore │  │   Gateway   │  │ • Camera (Future)    │ │ │
│  │  │ • Storage   │  │ • UPI       │  │ • GPS (Future)       │ │ │
│  │  │ • Messaging │  │   Integration│  │ • Biometrics         │ │ │
│  │  └─────────────┘  └─────────────┘  └──────────────────────┘ │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

### **2.2 Backend Architecture**

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLOUD FUNCTIONS                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────┐ │
│  │   AUTH          │    │   ORDER         │    │   PAYMENT    │ │
│  │   FUNCTIONS     │    │   FUNCTIONS     │    │   FUNCTIONS  │ │
│  │                 │    │                 │    │              │ │
│  │ • User Login    │    │ • Create Order  │    │ • Create     │ │
│  │ • Role Check    │    │ • Update Status │    │   Payment    │ │
│  │ • Token Verify  │    │ • Get Orders    │    │ • Verify     │ │
│  │ • Security      │    │ • Cancel Order  │    │   Payment    │ │
│  └─────────────────┘    └─────────────────┘    └──────────────┘ │
│                                                                 │
│  ┌─────────────────┐    ┌─────────────────┐    ┌──────────────┐ │
│  │   MENU          │    │   NOTIFICATION  │    │   WEBHOOK    │ │
│  │   FUNCTIONS     │    │   FUNCTIONS     │    │   HANDLERS   │ │
│  │                 │    │                 │    │              │ │
│  │ • Update Menu   │    │ • Send Push     │    │ • Payment    │ │
│  │ • Get Menu      │    │   Notifications │    │   Webhooks   │ │
│  │ • Toggle Item   │    │ • Order Alerts  │    │ • Status     │ │
│  │   Availability  │    │ • Promotions    │    │   Updates    │ │
│  └─────────────────┘    └─────────────────┘    └──────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      CLOUD FIRESTORE                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                    DATABASE SCHEMA                          │ │
│  │                                                             │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────┐ │ │
│  │  │    USERS    │  │   CANTEENS  │  │        ORDERS        │ │ │
│  │  │             │  │             │  │                      │ │ │
│  │  │ • userId    │  │ • canteenId │  │ • orderId            │ │ │
│  │  │ • rollNo    │  │ • name      │  │ • userId             │ │ │
│  │  │ • name      │  │ • location  │  │ • canteenId          │ │ │
│  │  │ • email     │  │ • contact   │  │ • items              │ │ │
│  │  │ • role      │  │ • isActive  │  │ • status             │ │ │
│  │  │ • createdAt │  │ • settings  │  │ • totalAmount        │ │ │
│  │  └─────────────┘  └─────────────┘  └──────────────────────┘ │ │
│  │                                                             │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌──────────────────────┐ │ │
│  │  │    MENU     │  │  PAYMENTS   │  │      SYSTEM          │ │ │
│  │  │             │  │             │  │                      │ │ │
│  │  │ • itemId    │  │ • paymentId │  │ • logs               │ │ │
│  │  │ • name      │  │ • orderId   │  │ • analytics          │ │ │
│  │  │ • price     │  │ • amount    │  │ • config             │ │ │
│  │  │ • available │  │ • status    │  │ • backups            │ │ │
│  │  │ • category  │  │ • method    │  │                      │ │ │
│  │  └─────────────┘  └─────────────┘  └──────────────────────┘ │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

---

## **3. DATA FLOW DIAGRAMS**

### **3.1 Order Placement Flow**

```
Student App          Firebase Auth        Firestore DB        Razorpay
     │                    │                    │                 │
     │ 1. Login Request   │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │ 2. Auth Token      │                 │
     │                    ├──────────────────► │                 │
     │                    │                    │                 │
     │ 3. Browse Menu     │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │                    │ 4. Get Menu     │
     │                    │                    ├────────────────►│
     │                    │                    │                 │
     │ 5. Add to Cart     │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │                    │                 │
     │ 6. Place Order     │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │                    │ 7. Save Order   │
     │                    │                    ├────────────────►│
     │                    │                    │                 │
     │                    │                    │ 8. Create       │
     │                    │                    │    Payment      │
     │                    │                    ├────────────────►│
     │                    │                    │                 │
     │ 9. Payment URL     │                    │                 │
     │◄─────────────────── │                    │                 │
     │                    │                    │                 │
     │ 10. Complete       │                    │                 │
     │    Payment         │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │                    │                 │
     │                    │                    │ 11. Update      │
     │                    │                    │     Order Status│
     │                    │                    ├────────────────►│
     │                    │                    │                 │
     │ 12. Order Confirmed│                    │                 │
     │◄─────────────────── │                    │                 │
```

### **3.2 Order Management Flow**

```
Canteen App          Cloud Functions       Firestore DB        Notifications
     │                    │                    │                 │
     │ 1. Login Request   │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │ 2. Verify Role     │                 │
     │                    ├──────────────────► │                 │
     │                    │                    │                 │
     │ 3. View Orders     │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │ 4. Get Pending     │                 │
     │                    │    Orders          │                 │
     │                    ├──────────────────► │                 │
     │                    │                    │                 │
     │ 5. Update Status   │                    │                 │
     ├──────────────────► │                    │                 │
     │                    │ 6. Update Order    │                 │
     │                    │    Status          │                 │
     │                    ├──────────────────► │                 │
     │                    │                    │                 │
     │                    │                    │ 7. Trigger      │
     │                    │                    │    Notification │
     │                    │                    ├────────────────►│
     │                    │                    │                 │
     │ 8. Notification    │                    │                 │
     │    Sent            │                    │                 │
     │◄─────────────────── │                    │                 │
```

---

## **4. TECHNOLOGY STACK JUSTIFICATION**

### **4.1 Frontend: Expo React Native**

**Why React Native?**
- **Cross-platform**: Single codebase for iOS and Android
- **Cost-effective**: No need for separate iOS/Android developers
- **Fast development**: Hot reloading and component reusability
- **Large ecosystem**: Extensive library support
- **Performance**: Near-native performance for our use case

**Why Expo?**
- **Zero configuration**: Simplified setup and deployment
- **Development speed**: Built-in tools and services
- **Testing**: Easy testing on physical devices
- **Updates**: Over-the-air updates without app store approval

**Alternatives Considered:**
- **Native iOS/Android**: Rejected due to higher development cost
- **Flutter**: Considered but React Native has better ecosystem
- **Progressive Web App**: Rejected due to limited device integration

### **4.2 Backend: Firebase**

**Why Firebase?**
- **Zero infrastructure**: No server management required
- **Free tier**: Generous free limits suitable for college startup
- **Real-time**: Built-in real-time database and notifications
- **Scalability**: Automatic scaling without configuration
- **Security**: Built-in authentication and security rules
- **Speed**: Fast development with minimal backend code

**Why Firestore over SQL?**
- **Flexible schema**: Easy to modify as requirements evolve
- **Real-time updates**: Native real-time capabilities
- **No joins needed**: Denormalized data fits our use case
- **Mobile optimized**: Designed for mobile app usage patterns

**Alternatives Considered:**
- **AWS Amplify**: More complex, higher cost
- **Traditional Node.js + MongoDB**: Requires server management
- **Supabase**: Considered but Firebase has better mobile integration

### **4.3 Payments: Razorpay**

**Why Razorpay?**
- **UPI support**: Comprehensive UPI integration
- **Split payments**: Built-in platform fee handling
- **Developer friendly**: Good React Native SDK
- **Indian market**: Optimized for Indian payment methods
- **Compliance**: PCI DSS compliant

**Alternatives Considered:**
- **PhonePe/Google Pay Direct**: More complex integration
- **Stripe**: Limited UPI support in India
- **Paytm**: Less developer-friendly APIs

---

## **5. SCALABILITY PLAN**

### **5.1 Horizontal Scaling Strategy**

**Phase 1: Single College (0-1000 users)**
- Firebase free tier sufficient
- Single canteen integration
- Manual monitoring and support

**Phase 2: Multiple Colleges (1000-10,000 users)**
- Firebase paid tier for increased limits
- Multiple canteen integrations
- Automated monitoring setup
- Basic analytics implementation

**Phase 3: University Network (10,000+ users)**
- Firebase enterprise features
- Dedicated support
- Advanced analytics and monitoring
- Potential migration to custom backend

### **5.2 Performance Optimization**

**Database Optimization:**
- Indexing for frequently queried fields
- Query optimization and caching
- Data denormalization for read performance
- Batch operations for bulk updates

**App Performance:**
- Image compression and lazy loading
- Code splitting and bundle optimization
- Local caching for frequently accessed data
- Background sync for offline functionality

**Network Optimization:**
- CDN for static assets
- API response compression
- Connection pooling
- Retry mechanisms for failed requests

---

## **6. SECURITY ARCHITECTURE**

### **6.1 Authentication Flow**

```
User Login Request
        │
        ▼
Firebase Authentication
        │
        ▼
JWT Token Generation
        │
        ▼
Role-Based Access Control
        │
        ▼
Secure API Access
```

### **6.2 Data Protection Layers**

**Application Layer:**
- Input validation and sanitization
- Secure coding practices
- Error handling without information leakage
- Session management

**Network Layer:**
- HTTPS enforcement
- Certificate pinning
- API rate limiting
- DDoS protection (Firebase built-in)

**Database Layer:**
- Firebase security rules
- Field-level permissions
- Data encryption at rest
- Access logging and monitoring

**Payment Layer:**
- PCI DSS compliance (handled by Razorpay)
- Tokenization of payment data
- Secure payment flow
- Webhook signature verification

---

## **7. MONITORING & MAINTENANCE**

### **7.1 Monitoring Strategy**

**Application Monitoring:**
- Firebase Crashlytics for error tracking
- Performance monitoring for app speed
- User analytics for feature usage
- Custom metrics for business KPIs

**Infrastructure Monitoring:**
- Firebase usage dashboard
- Database performance metrics
- API response time monitoring
- Storage usage tracking

**Business Monitoring:**
- Order volume tracking
- Payment success rates
- User engagement metrics
- Revenue tracking

### **7.2 Maintenance Plan**

**Daily:**
- Monitor error rates and performance
- Check payment processing status
- Review user feedback and support requests

**Weekly:**
- Analyze usage patterns and trends
- Review database performance
- Update menu items and canteen info
- Test payment flows

**Monthly:**
- Security review and updates
- Performance optimization review
- Feature usage analysis
- Plan improvements based on metrics

---
Would you like me to proceed with the **Business Model Canvas** next?
