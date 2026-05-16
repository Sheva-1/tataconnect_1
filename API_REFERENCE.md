# TataConnect API Reference

## Base URL
```
http://localhost:4000/api
```

## Authentication
Include JWT token in Authorization header:
```
Authorization: Bearer {JWT_TOKEN}
```

---

## Authentication Endpoints

### Sign Up
Create a new user account

**Endpoint:** `POST /auth/sign-up`

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123",
  "role": "FAMILY",
  "nicNumber": "CM123456",
  "nicDocumentUrl": "https://example.com/nic.jpg",
  "videoUrl": "https://example.com/video.mp4",
  "whatsappNumber": "+237123456789",
  "fullName": "John Doe",
  "city": "Yaoundé",
  "town": "Mendong",
  "rateFcfa": "5000",
  "bio": "Experienced caregiver",
  "languages": "French,English",
  "services": "CHILDCARE,HOUSEKEEPING"
}
```

**Response:**
```json
{
  "user": {
    "id": "user-id",
    "email": "user@example.com",
    "role": "FAMILY",
    "caregiver": null
  },
  "token": "jwt-token"
}
```

### Login
Authenticate user and get JWT token

**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "user": {
    "id": "user-id",
    "email": "user@example.com",
    "role": "FAMILY"
  },
  "token": "jwt-token"
}
```

### Forgot Password
Request password reset email

**Endpoint:** `POST /auth/forgot-password`

**Request Body:**
```json
{
  "email": "user@example.com"
}
```

**Response:**
```json
{
  "message": "Password reset link sent to email"
}
```

### Reset Password
Update password with reset token

**Endpoint:** `POST /auth/reset-password`

**Request Body:**
```json
{
  "token": "reset-token",
  "password": "new-password"
}
```

### Verify Token
Validate JWT token and get current user

**Endpoint:** `GET /auth/verify-token`

**Headers:**
```
Authorization: Bearer {JWT_TOKEN}
```

**Response:**
```json
{
  "user": {
    "id": "user-id",
    "email": "user@example.com",
    "role": "FAMILY"
  }
}
```

---

## Caregiver Endpoints

### Get Caregiver Profile
Retrieve caregiver profile by user ID

**Endpoint:** `GET /caregivers/:userId`

**Response:**
```json
{
  "id": "caregiver-id",
  "userId": "user-id",
  "fullName": "Marie Dupont",
  "city": "Yaoundé",
  "town": "Mendong",
  "bio": "Experienced nanny...",
  "rating": 4.7,
  "reviews": 12,
  "rateFcfa": "5000",
  "languages": ["French", "English"],
  "services": ["CHILDCARE", "HOUSEKEEPING"],
  "verificationStatus": "VERIFIED",
  "documents": [...]
}
```

### Update Caregiver Profile
Modify caregiver information and re-index in Pinecone

**Endpoint:** `PUT /caregivers/:userId`

**Request Body:**
```json
{
  "bio": "Updated bio...",
  "rateFcfa": "6000",
  "availability": "{\"monday\": \"09:00-17:00\"}"
}
```

### Submit Documents for Verification
Upload documents (ID, certificates, references)

**Endpoint:** `POST /caregivers/:userId/documents`

**Request Body:**
```json
{
  "type": "ID",
  "fileUrl": "https://example.com/id.jpg",
  "fileName": "national-id.jpg"
}
```

### Submit Profile for Verification
Request profile verification review

**Endpoint:** `POST /caregivers/:userId/submit-verification`

**Response:**
```json
{
  "id": "caregiver-id",
  "verificationStatus": "PENDING",
  "submittedAt": "2024-05-15T10:30:00Z"
}
```

### List All Caregivers
Get verified caregivers with optional filtering

**Endpoint:** `GET /caregivers`

**Query Parameters:**
- `city` (string) - Filter by city
- `minRating` (number) - Minimum rating
- `limit` (number, default: 20) - Results per page

**Response:**
```json
[
  {
    "id": "caregiver-id",
    "fullName": "Marie Dupont",
    "city": "Yaoundé",
    "rating": 4.7,
    "rateFcfa": "5000"
  }
]
```

### Semantic Search Caregivers
AI-powered natural language search

**Endpoint:** `POST /caregivers/search/semantic`

**Request Body:**
```json
{
  "query": "nanny who speaks French and likes dogs in Yaoundé",
  "city": "Yaoundé",
  "services": "CHILDCARE",
  "languages": "French,English",
  "limit": 10
}
```

**Response:**
```json
[
  {
    "id": "caregiver-id",
    "fullName": "Marie Dupont",
    "bio": "...",
    "score": 0.89
  }
]
```

---

## Booking Endpoints

### Create Booking
Create a new booking request

**Endpoint:** `POST /bookings`

**Request Body:**
```json
{
  "familyId": "family-user-id",
  "caregiverId": "caregiver-user-id",
  "title": "Weekly Childcare",
  "description": "Monday to Friday, 8am-5pm",
  "startDate": "2024-06-01T08:00:00Z",
  "endDate": "2024-06-30T17:00:00Z",
  "location": "Yaoundé, Mendong",
  "rateFcfa": 125000
}
```

**Response:**
```json
{
  "id": "booking-id",
  "status": "PENDING",
  "rateFcfa": "125000",
  "createdAt": "2024-05-15T10:30:00Z"
}
```

### Create Payment Intent
Initialize Stripe payment for booking

**Endpoint:** `POST /bookings/:bookingId/payment`

**Request Body:**
```json
{
  "amountXAF": 125000
}
```

**Response:**
```json
{
  "clientSecret": "pi_xxx_secret_xxx",
  "paymentIntentId": "pi_xxx",
  "amount": 125000
}
```

### Confirm Payment
Verify payment and update booking status

**Endpoint:** `POST /bookings/:bookingId/confirm-payment`

**Request Body:**
```json
{
  "paymentIntentId": "pi_xxx"
}
```

**Response:**
```json
{
  "id": "booking-id",
  "status": "CONFIRMED",
  "stripePaymentId": "pi_xxx"
}
```

### Get User Bookings
List bookings for current user (as family or caregiver)

**Endpoint:** `GET /bookings/user/:userId`

**Response:**
```json
[
  {
    "id": "booking-id",
    "title": "Weekly Childcare",
    "status": "CONFIRMED",
    "rateFcfa": "125000",
    "startDate": "2024-06-01T08:00:00Z"
  }
]
```

### Get Booking Details
Retrieve specific booking information

**Endpoint:** `GET /bookings/:bookingId`

**Response:**
```json
{
  "id": "booking-id",
  "familyId": "family-id",
  "caregiverId": "caregiver-id",
  "title": "Weekly Childcare",
  "description": "...",
  "status": "CONFIRMED",
  "rateFcfa": "125000"
}
```

### Update Booking Status
Change booking status

**Endpoint:** `PUT /bookings/:bookingId`

**Request Body:**
```json
{
  "status": "COMPLETED"
}
```

**Valid Statuses:** `PENDING`, `CONFIRMED`, `REJECTED`, `COMPLETED`, `CANCELLED`

---

## Admin Endpoints

**Note:** Requires `X-Admin-Id` header with admin user ID

### Get Pending Verifications
List caregivers awaiting verification

**Endpoint:** `GET /admin/verification/pending`

**Response:**
```json
[
  {
    "id": "caregiver-id",
    "fullName": "Marie Dupont",
    "verificationStatus": "PENDING",
    "documents": [...]
  }
]
```

### Get Verification Statistics
Dashboard statistics

**Endpoint:** `GET /admin/verification/stats`

**Response:**
```json
{
  "total": 25,
  "verified": 20,
  "pending": 3,
  "rejected": 2,
  "approvalRate": 80
}
```

### Approve Caregiver
Approve caregiver profile for platform

**Endpoint:** `POST /admin/verification/:caregiverId/approve`

**Request Body:**
```json
{
  "adminId": "admin-user-id",
  "notes": "All documents verified"
}
```

### Reject Caregiver
Reject caregiver with reason

**Endpoint:** `POST /admin/verification/:caregiverId/reject`

**Request Body:**
```json
{
  "adminId": "admin-user-id",
  "rejectionReason": "Document quality insufficient"
}
```

### List All Caregivers (Admin)
Browse all caregivers with filters

**Endpoint:** `GET /admin/verification`

**Query Parameters:**
- `status` - Filter by verification status
- `city` - Filter by city
- `page` - Page number
- `limit` - Results per page

### Get Verification Details
View complete verification history

**Endpoint:** `GET /admin/verification/:caregiverId/verification`

**Response:**
```json
{
  "id": "caregiver-id",
  "fullName": "Marie Dupont",
  "documents": [...],
  "verifications": [
    {
      "status": "VERIFIED",
      "verifiedByAdmin": "admin-email",
      "notes": "...",
      "completedAt": "2024-05-10T10:00:00Z"
    }
  ]
}
```

---

## Messaging Endpoints

### Create or Get Conversation
Start conversation between two users

**Endpoint:** `POST /messages/conversations`

**Request Body:**
```json
{
  "initiatedById": "user-id-1",
  "receivedById": "user-id-2",
  "subject": "Childcare inquiry"
}
```

**Response:**
```json
{
  "id": "conversation-id",
  "initiatedById": "user-id-1",
  "receivedById": "user-id-2",
  "subject": "Childcare inquiry"
}
```

### Get User Conversations
List all conversations for user

**Endpoint:** `GET /messages/user/:userId`

**Response:**
```json
[
  {
    "id": "conversation-id",
    "initiatedBy": {
      "id": "user-id",
      "email": "user@example.com"
    },
    "lastMessage": "Thank you for the inquiry",
    "lastMessageAt": "2024-05-15T10:30:00Z"
  }
]
```

### Get Messages in Conversation
Retrieve message history

**Endpoint:** `GET /messages/conversation/:conversationId/messages`

**Response:**
```json
[
  {
    "id": "message-id",
    "senderId": "user-id",
    "body": "Hi, are you available?",
    "createdAt": "2024-05-15T10:00:00Z"
  }
]
```

### Send Message
Post new message in conversation

**Endpoint:** `POST /messages/conversation/:conversationId/messages`

**Request Body:**
```json
{
  "senderId": "user-id",
  "body": "Yes, I'm available starting Monday!"
}
```

**Response:**
```json
{
  "id": "message-id",
  "conversationId": "conversation-id",
  "senderId": "user-id",
  "body": "Yes, I'm available starting Monday!",
  "createdAt": "2024-05-15T10:30:00Z"
}
```

### Mark Conversation as Read
Update last read timestamp

**Endpoint:** `PUT /messages/conversation/:conversationId/read`

### Delete Conversation
Remove conversation and all messages

**Endpoint:** `DELETE /messages/conversation/:conversationId`

**Response:**
```json
{
  "message": "Conversation deleted"
}
```

---

## Error Responses

### 400 Bad Request
```json
{
  "error": {
    "fieldName": ["Validation error message"]
  }
}
```

### 401 Unauthorized
```json
{
  "error": "Invalid credentials"
}
```

### 403 Forbidden
```json
{
  "error": "Unauthorized access"
}
```

### 404 Not Found
```json
{
  "error": "Resource not found"
}
```

### 500 Server Error
```json
{
  "error": "Internal server error"
}
```

---

## Rate Limiting
- Standard: 100 requests/hour
- Search: 50 requests/hour
- Payment: 10 requests/minute

## Response Headers
```
Content-Type: application/json
X-Request-Id: unique-request-id
Cache-Control: public, max-age=300
```

---

**Last Updated:** May 2024
**API Version:** 1.0.0
