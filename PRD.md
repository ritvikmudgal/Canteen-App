---

# **PRODUCT REQUIREMENTS DOCUMENT (PRD)**
## **Campus Canteen Order App**

**Version:** 1.0  
**Date:** February 28, 2026  
**Prepared for:** College Startup Team  
**Target Launch:** 6 Months

---

## **1. EXECUTIVE SUMMARY**

### **Product Vision**
Create a seamless mobile ordering platform that eliminates canteen queues by allowing students to order and pay for food online, with real-time order tracking and instant notifications when food is ready.

### **Problem Statement**
- Long queues in college canteens waste student time
- Students miss classes due to waiting for food
- Canteen staff overwhelmed during peak hours
- No reliable way to track order status
- Payment delays and cash handling issues

### **Solution Overview**
A mobile app that allows students to:
- Browse canteen menu and place orders
- Pay online via UPI
- Track order status in real-time
- Receive instant notifications when food is ready
- Pick up orders using order number/PIN

---

## **2. USER PERSONAS**

### **Primary User: Student (Ages 18-25)**

**Name:** Rohan Sharma  
**Year:** 2nd Year Engineering  
**Pain Points:**
- Spends 20-30 minutes in canteen queues daily
- Misses class starts due to long waits
- Forgets to carry cash frequently
- Can't track if his order is being prepared

**Goals:**
- Order food quickly between classes
- Pay online without carrying cash
- Know exactly when food will be ready
- Avoid missing classes due to canteen queues

**Tech Profile:**
- Uses Android smartphone daily
- Comfortable with UPI payments (PhonePe, GPay)
- Active on WhatsApp, Instagram
- Downloads apps for convenience

---

### **Secondary User: Canteen Staff (Ages 30-50)**

**Name:** Mr. Patel  
**Role:** Canteen Manager  
**Pain Points:**
- Overwhelmed during lunch rush (12-2 PM)
- Difficulty managing multiple orders simultaneously
- Cash handling and reconciliation issues
- No way to inform students when orders are ready

**Goals:**
- Manage orders efficiently during peak hours
- Reduce order confusion and errors
- Get advance notice of incoming orders
- Streamline payment collection

**Tech Profile:**
- Basic smartphone user
- Familiar with WhatsApp
- Needs simple, intuitive interface
- Prefers minimal training

---

## **3. FUNCTIONAL REQUIREMENTS**

### **3.1 User Registration & Authentication**

**FR-001: Student Registration**
- **Description:** Students can register using college roll number and password
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Roll number format validation (e.g., "CS2023001")
  - Password minimum 6 characters
  - Email verification optional for college pilot
  - Profile creation with name and contact info

**FR-002: Canteen Staff Authentication**
- **Description:** Canteen staff can login with special credentials provided by app admin
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Separate login screen for canteen staff
  - Admin-controlled credential distribution
  - Role-based access control

**FR-003: Secure Logout**
- **Description:** Users can securely logout from the app
- **Priority:** P2 (Should Have)
- **Acceptance Criteria:**
  - One-tap logout functionality
  - Session timeout after 15 minutes of inactivity

---

### **3.2 Menu Management**

**FR-004: Menu Display**
- **Description:** Students can view current canteen menu with prices and descriptions
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Categorized menu (Breakfast, Lunch, Snacks, Beverages)
  - High-quality food images
  - Price display for each item
  - Availability status (Available/Not Available)

**FR-005: Menu Updates (Canteen Staff)**
- **Description:** Canteen staff can update menu item availability in real-time
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Toggle availability for each menu item
  - Instant update visible to all users
  - Option to mark items as "Running Low"

---

### **3.3 Order Management**

**FR-006: Order Placement**
- **Description:** Students can add items to cart and place orders
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Add/remove items from cart
  - View order summary with total amount
  - Platform fee calculation (₹2 if order ≥ ₹50, ₹3 if < ₹50)
  - Order confirmation with unique order number

**FR-007: Order Queue Management**
- **Description:** System manages order queue and provides estimated preparation time
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Display queue position (e.g., "You are #3 in queue")
  - Estimated preparation time (5-15 minutes)
  - Real-time queue updates

**FR-008: Order Status Tracking**
- **Description:** Students can track their order status in real-time
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Order status: Received → Preparing → Ready
  - Push notifications for status changes
  - Order ready notification with pickup instructions

---

### **3.4 Payment Processing**

**FR-009: UPI Payment Integration**
- **Description:** Students can pay for orders using any UPI app
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Integration with major UPI apps (PhonePe, GPay, Paytm)
  - Secure payment gateway (Razorpay recommended)
  - Payment confirmation and receipt
  - Failed payment handling and retry

**FR-010: Platform Fee Calculation**
- **Description:** System automatically calculates and collects platform fees
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Platform fee: ₹2 if order ≥ ₹50, ₹3 if order < ₹50
  - Fee added to customer bill
  - Automatic split: Customer → Platform + Canteen
  - Weekly payout to platform account

---

### **3.5 Canteen Staff Interface**

**FR-011: Order Dashboard**
- **Description:** Canteen staff can view and manage incoming orders
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - List of pending orders with details
  - Order preparation status updates
  - Order completion marking
  - Order history and analytics

**FR-012: Order Notifications**
- **Description:** Canteen staff receive instant notifications for new orders
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Push notification for new orders
  - Sound alert for order notifications
  - Order details display (items, quantity, special instructions)

---

### **3.6 Order Pickup**

**FR-013: Order Pickup Verification**
- **Description:** Students can verify order pickup using order number or PIN
- **Priority:** P1 (Must Have)
- **Acceptance Criteria:**
  - Display order number and QR code
  - Canteen staff can verify order using number/PIN
  - Order marked as completed after pickup
  - Pickup confirmation notification to student

---

## **4. NON-FUNCTIONAL REQUIREMENTS**

### **4.1 Performance Requirements**

**NFR-001: Response Time**
- **Requirement:** App should respond to user actions within 2 seconds
- **Priority:** P1 (Must Have)
- **Rationale:** User experience and retention

**NFR-002: Concurrent Users**
- **Requirement:** Support 500+ concurrent users during peak hours
- **Priority:** P2 (Should Have)
- **Rationale:** College lunch rush scenarios

**NFR-003: Order Processing**
- **Requirement:** Order placement should complete within 5 seconds
- **Priority:** P1 (Must Have)
- **Rationale:** Quick service during class breaks

### **4.2 Security Requirements**

**NFR-004: Data Security**
- **Requirement:** All user data and payment information must be encrypted
- **Priority:** P1 (Must Have)
- **Rationale:** Compliance and user trust

**NFR-005: Payment Security**
- **Requirement:** PCI DSS compliance for payment processing
- **Priority:** P1 (Must Have)
- **Rationale:** Legal requirement for payment handling

**NFR-006: Authentication Security**
- **Requirement:** Secure password storage and transmission
- **Priority:** P1 (Must Have)
- **Rationale:** User account protection

### **4.3 Usability Requirements**

**NFR-007: User Interface**
- **Requirement:** App should be usable by users with basic smartphone skills
- **Priority:** P1 (Must Have)
- **Rationale:** Canteen staff with varying tech proficiency

**NFR-008: Accessibility**
- **Requirement:** App should support basic accessibility features
- **Priority:** P2 (Should Have)
- **Rationale:** Inclusive design principles

### **4.4 Reliability Requirements**

**NFR-009: Uptime**
- **Requirement:** 99% uptime during college hours (9 AM - 5 PM)
- **Priority:** P1 (Must Have)
- **Rationale:** Critical service during college operations

**NFR-010: Data Backup**
- **Requirement:** Automatic daily backup of all order and user data
- **Priority:** P2 (Should Have)
- **Rationale:** Data recovery and business continuity

---

## **5. SUCCESS METRICS**

### **5.1 User Adoption Metrics**

- **Target:** 500+ student registrations in first month
- **Target:** 100+ orders per day by month 3
- **Target:** 80% order completion rate (students actually pick up orders)

### **5.2 Business Metrics**

- **Target:** Break-even by month 4 (covering payment gateway fees)
- **Target:** 20% month-over-month growth in order volume
- **Target:** Average order value of ₹80-100

### **5.3 User Experience Metrics**

- **Target:** App rating of 4.0+ on app stores
- **Target:** Order placement time under 2 minutes
- **Target:** 95% on-time order completion rate

### **5.4 Technical Metrics**

- **Target:** App load time under 3 seconds
- **Target:** Payment success rate of 95%+
- **Target:** Notification delivery rate of 98%+

---

## **6. ASSUMPTIONS & CONSTRAINTS**

### **6.1 Assumptions**

- College administration will support the initiative
- Canteen owners will participate in the pilot program
- Students have access to smartphones and UPI apps
- Internet connectivity is available in canteen areas
- Initial launch will be in one college only

### **6.2 Constraints**

- **Budget:** Zero development budget (using free tiers and student resources)
- **Timeline:** 6-month development and launch timeline
- **Team:** Student developers with limited professional experience
- **Legal:** No formal business registration for college pilot phase
- **Scale:** Initial focus on single college with 2-3 canteens

### **6.3 Dependencies**

- Firebase free tier availability
- Razorpay payment gateway integration
- College WiFi connectivity
- Canteen staff cooperation and training
- Student body promotion and adoption

---

## **7. OUT OF SCOPE**

### **Phase 1 (MVP) - Not Included**

- Multiple college support
- Advanced analytics and reporting
- Loyalty programs and rewards
- Advanced payment methods (cards, wallets)
- Delivery service integration
- Advanced menu customization
- Multi-language support
- Integration with college ID systems

---

## **8. RISK ASSESSMENT**

### **High Risk**

- **Payment Gateway Integration Complexity**
  - Mitigation: Use established providers like Razorpay with good documentation

- **User Adoption by Canteen Staff**
  - Mitigation: Simple interface design and hands-on training

### **Medium Risk**

- **Network Connectivity Issues**
  - Mitigation: Offline order caching and sync when connection restored

- **Competition from Existing Solutions**
  - Mitigation: Focus on college-specific features and relationships

### **Low Risk**

- **App Store Approval**
  - Mitigation: Follow platform guidelines and test thoroughly

---

This PRD provides the foundation for your canteen app development. It focuses on the core MVP features needed for a successful college launch while keeping costs and complexity minimal.

Would you like me to proceed with the **Technical Requirements Document (TRD)** next?
