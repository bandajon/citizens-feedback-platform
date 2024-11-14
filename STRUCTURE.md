# **Updated Backend Architectural Design for the Zambia Citizen Feedback Platform**

## **Table of Contents**

1. [Introduction](#introduction)
2. [Architectural Overview](#architectural-overview)
3. [Core Components](#core-components)
   - [1. API Layer](#1-api-layer)
   - [2. Business Logic Layer](#2-business-logic-layer)
   - [3. Data Access Layer](#3-data-access-layer)
   - [4. External Integrations](#4-external-integrations)
4. [Detailed Component Design](#detailed-component-design)
   - [1. User Authentication and Management](#1-user-authentication-and-management)
   - [2. Feedback Submission and Management](#2-feedback-submission-and-management)
   - [3. Location Data Handling](#3-location-data-handling)
   - [4. Anonymity and Data Privacy](#4-anonymity-and-data-privacy)
   - [5. Data Models (Database Schemas)](#5-data-models-database-schemas)
   - [6. API Endpoints](#6-api-endpoints)
   - [7. Integration with External APIs](#7-integration-with-external-apis)
   - [8. Security Considerations](#8-security-considerations)
   - [9. Scalability and Performance](#9-scalability-and-performance)
   - [10. Error Handling and Logging](#10-error-handling-and-logging)
   - [11. Testing Strategies](#11-testing-strategies)
   - [12. Deployment Considerations](#12-deployment-considerations)
5. [Conclusion](#conclusion)

---

## **Introduction**

In response to the requirement that users need not always register to submit feedback, this updated architectural design accommodates unregistered users while ensuring that essential data—such as location, age, and gender—is still captured. The system allows for both registered and unregistered users to submit feedback, with options for anonymity, and maintains robust security and privacy standards.

---

## **Architectural Overview**

The backend architecture remains a **modular monolithic** application developed in **Go (Golang)**, exposing RESTful APIs for client consumption. Key adjustments include:

- **Optional User Registration**: Users can submit feedback without registering.
- **Mandatory Data Capture**: Age, gender, and location are required fields for all feedback submissions.
- **Enhanced Anonymity Handling**: Anonymity options are available for both registered and unregistered users.
- **Data Integrity and Privacy**: Personal data is handled securely, and anonymous submissions are properly segregated.

---

## **Core Components**

### **1. API Layer**

- **Framework**: Gin (efficient HTTP request handling).
- **Endpoints**: RESTful APIs supporting authenticated and unauthenticated requests.
- **Middleware**:
  - **Authentication Middleware**: Applies to endpoints requiring authentication.
  - **Logging Middleware**: Logs requests and responses.
  - **CORS Middleware**: Manages cross-origin requests.
  - **Rate Limiting Middleware**: Prevents abuse, especially on open endpoints.

### **2. Business Logic Layer**

- **Modules**:
  - **User Management**: Handles optional registration and authentication.
  - **Feedback Management**: Manages feedback from both registered and unregistered users.
  - **Anonymity Handling**: Processes anonymous submissions while capturing essential demographic data.
  - **Location Services**: Manages geolocation data.
  - **External API Integrations**: Interfaces with OpenAI, Zamtel SMS, Google Maps APIs.

### **3. Data Access Layer**

- **ORM**: GORM for database operations.
- **Repositories**: Abstract data access for various entities.
- **Transactions**: Ensure data consistency during operations.

### **4. External Integrations**

- **OpenAI API**: For feedback classification and sentiment analysis.
- **Zamtel SMS API**: For SMS functionalities.
- **Google Maps API**: For geolocation services.

---

## **Detailed Component Design**

### **1. User Authentication and Management**

#### **Optional Registration**

- **Unregistered Users**:
  - Can submit feedback without creating an account.
  - Must provide age, gender, and location data with each submission.
- **Registered Users**:
  - Have the option to create an account using email, phone number, or NRC number.
  - Can track their submissions and receive personalized updates.
  - Can choose to submit feedback anonymously or with their identity.

#### **Authentication Flow**

- **Registration (Optional)**:
  - Collects minimal personal information.
  - Uses secure password practices (hashed passwords).
- **Login**:
  - Standard login for registered users.
  - Issues JWT tokens for session management.
- **Guest Access**:
  - Unregistered users access public endpoints without authentication.

#### **User Roles**

- **Guest**: Unregistered user submitting feedback.
- **Registered User**: Authenticated user with access to personal dashboard.
- **Admin**: Government officials managing feedback.
- **Moderator**: Platform administrators handling content moderation.

#### **Data Privacy**

- **Unregistered Users**:
  - No personal identifiers are stored.
  - Feedback linked to a unique anonymous feedback ID.
- **Registered Users**:
  - Personal data stored securely.
  - Option to submit feedback anonymously.

### **2. Feedback Submission and Management**

#### **Feedback Submission Workflow**

1. **Data Collection**:
   - **Mandatory Fields for All Users**:
     - **Age**: Numeric value.
     - **Gender**: Selected from predefined options.
     - **Location Data**: Captured automatically or entered manually.
     - **Category**: Selected from predefined categories.
     - **Content**: Textual description of the feedback.
   - **Optional Fields**:
     - **Contact Information**: For unregistered users who wish to provide it (e.g., phone number for updates).

2. **Anonymity Options**:
   - **Unregistered Users**:
     - Automatically considered anonymous, as no personal accounts are created.
   - **Registered Users**:
     - Can choose to submit feedback anonymously or with their identity.

3. **Data Validation and Sanitization**:
   - Ensures required fields are present.
   - Sanitizes inputs to prevent security vulnerabilities.

4. **Location Data Handling**:
   - **Automatic Capture**: With user consent, captures GPS coordinates.
   - **Manual Entry**: Users can enter their location details.
   - **Reverse Geocoding**: Converts coordinates to readable addresses.

5. **Feedback Processing**:
   - **Classification and Sentiment Analysis**: Utilizes OpenAI API.
   - **Storage**: Saves feedback with associated metadata.

6. **Acknowledgment**:
   - **Unregistered Users**: Receive a reference number for their submission.
   - **Registered Users**: Can view and track their submissions in their account.

#### **Feedback Retrieval and Management**

- **Registered Users**:
  - Access to their submission history (excluding anonymous submissions).
  - Receive notifications and updates.

- **Unregistered Users**:
  - Can use their feedback reference number to check status via a public endpoint or SMS.

- **Admins and Moderators**:
  - Access all feedback submissions.
  - Tools for filtering, searching, and updating feedback statuses.

### **3. Location Data Handling**

#### **Capturing Location Data**

- **User Consent**:
  - Explicit consent required for automatic location capture.
- **Automatic Geolocation**:
  - Uses device capabilities to obtain GPS data.
- **Manual Entry**:
  - Users can select their location from a map or enter address details.

#### **Geocoding and Mapping**

- **Geocoding Services**:
  - Utilizes Google Maps Geocoding API.
- **Data Storage**:
  - Stores both coordinates and human-readable addresses.

#### **Privacy Considerations**

- **Anonymity**:
  - Location data is stored without linking to personal identifiers for unregistered and anonymous users.

### **4. Anonymity and Data Privacy**

#### **Anonymous Reporting**

- **Unregistered Users**:
  - No account creation; submissions are anonymous by default.
  - Age and gender are required for demographic analysis.
  - Assigned a unique feedback reference number.

- **Registered Users**:
  - Can opt to submit feedback anonymously.
  - Their identity is not linked to the anonymous feedback.

#### **Data Handling and Storage**

- **Separation of Personal Data**:
  - Feedback data stored separately from any personal data.
- **Data Access Control**:
  - Strict permissions ensure only authorized personnel can access sensitive data.

#### **Legal Compliance**

- **Data Protection Laws**:
  - Adheres to Zambia's Data Protection Act.
- **User Rights**:
  - Users can request deletion of their data where applicable.

### **5. Data Models (Database Schemas)**

#### **Key Entities**

1. **Feedback**
   - `id`: UUID
   - `user_id`: UUID (nullable, only for registered users who are not anonymous)
   - `age`: Integer
   - `gender`: String (enum)
   - `category`: String (enum)
   - `content`: Text
   - `location`: Geography(Point)
   - `address`: String
   - `classification`: String
   - `sentiment`: String
   - `status`: String (enum)
   - `created_at`: Timestamp
   - `updated_at`: Timestamp
   - `reference_number`: String (unique, for unregistered users to track submissions)

2. **User** (for registered users)
   - `id`: UUID
   - `name`: String (nullable)
   - `email`: String (nullable)
   - `phone_number`: String (nullable)
   - `password_hash`: String
   - `role`: String (enum)
   - `created_at`: Timestamp
   - `updated_at`: Timestamp

#### **Database Design Considerations**

- **Constraints**:
  - Ensure `age`, `gender`, and `location` are not null in `Feedback`.
- **Indexes**:
  - Index on `location`, `category`, `classification`, `sentiment` for efficient querying.

### **6. API Endpoints**

#### **Feedback Endpoints (Open to All Users)**

- **POST** `/api/v1/feedback`
  - Submits new feedback.
  - Accepts submissions from both registered and unregistered users.
- **GET** `/api/v1/feedback/status/{reference_number}`
  - Allows unregistered users to check the status of their submission using their reference number.

#### **User Authentication Endpoints (For Optional Registration)**

- **POST** `/api/v1/auth/register`
  - Registers a new user (optional).
- **POST** `/api/v1/auth/login`
  - Authenticates a registered user.

#### **User-Specific Feedback Endpoints (For Registered Users)**

- **GET** `/api/v1/users/me/feedback`
  - Retrieves the authenticated user's feedback submissions.
- **GET** `/api/v1/users/me/feedback/{id}`
  - Retrieves a specific feedback entry.

#### **Admin Endpoints**

- **GET** `/api/v1/admin/feedback`
  - Retrieves all feedback submissions.
- **PUT** `/api/v1/admin/feedback/{id}`
  - Updates feedback status.

### **7. Integration with External APIs**

#### **OpenAI API**

- **Classification and Sentiment Analysis**:
  - Invoked during feedback submission.
- **Implementation**:
  - Asynchronous processing to prevent blocking the user interface.

#### **Zamtel SMS API**

- **Sending SMS**:
  - Confirmation messages to unregistered users with their reference numbers.
  - Updates on feedback status if contact information is provided.
- **Receiving SMS**:
  - Enables users to submit feedback via SMS.
  - Parses SMS content to extract required data.

#### **Google Maps API**

- **Geocoding Services**:
  - Converts coordinates to addresses and vice versa.
- **Usage**:
  - During feedback submission and in admin dashboards for mapping.

### **8. Security Considerations**

#### **Authentication and Authorization**

- **Public Endpoints**:
  - Securely handle data from unregistered users.
- **Authentication Middleware**:
  - Protects endpoints that require user authentication.
- **Role-Based Access Control (RBAC)**:
  - Ensures only authorized users access specific functionalities.

#### **Data Protection**

- **Input Validation**:
  - Sanitize all inputs to prevent injection attacks.
- **Encryption**:
  - Use HTTPS for data in transit.
  - Secure storage of sensitive data.

#### **API Security**

- **Rate Limiting**:
  - Prevent abuse of public endpoints.
- **Audit Logging**:
  - Log critical actions for security auditing.

### **9. Scalability and Performance**

#### **Handling Increased Load**

- **Stateless APIs**:
  - Facilitate horizontal scaling.
- **Asynchronous Tasks**:
  - Use message queues for tasks like SMS sending and external API calls.

#### **Optimization**

- **Caching**:
  - Implement caching for frequently accessed data.
- **Database Tuning**:
  - Optimize queries and indexing strategies.

### **10. Error Handling and Logging**

#### **Error Responses**

- **Standardized Formats**:
  - Consistent error messages with HTTP status codes.

#### **Logging**

- **Detailed Logs**:
  - Include context for debugging.
- **Monitoring Tools**:
  - Integrate with monitoring solutions for real-time alerts.

### **11. Testing Strategies**

#### **Unit Testing**

- Test individual components and functions.

#### **Integration Testing**

- Validate interactions between components.

#### **End-to-End Testing**

- Simulate user interactions, including anonymous submissions.

### **12. Deployment Considerations**

#### **Containerization**

- Use Docker for consistent environments.

#### **CI/CD Pipelines**

- Automate build, test, and deployment processes.

#### **Environment Configuration**

- Securely manage environment variables and secrets.

---

## **Conclusion**

This updated architectural design accommodates the requirement that users need not always register to submit feedback. By allowing unregistered users to participate, the platform becomes more inclusive, enabling greater citizen engagement. The design ensures that essential demographic and location data are captured for all submissions, maintaining the platform's ability to provide valuable insights while upholding data privacy and security standards.

---

This comprehensive architectural design provides a clear roadmap for implementing the backend of the Zambia Citizen Feedback Platform, ensuring it meets the functional requirements and supports unregistered users effectively.