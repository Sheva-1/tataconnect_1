# TataConnect Implementation Guide

## Project Overview
TataConnect is a full-stack marketplace connecting verified housekeepers and nannies with families in Cameroon, with AI-powered semantic search and secure payment processing.

## ✅ Completed Implementation

### Backend Services (Fully Implemented)

#### 1. **Authentication Service** (`backend/src/services/auth.ts`)
- JWT token generation and verification
- Supabase integration for user management
- Role-based access control (FAMILY, CAREGIVER, ADMIN)
- User verification utilities

#### 2. **Pinecone Semantic Search** (`backend/src/services/pinecone.ts`)
- Vector embeddings for caregiver profiles
- Semantic search queries to find caregivers by natural language
- Hybrid filtering (vector + SQL filters)
- Profile indexing and management
- Supports searching by: city, services, languages

#### 3. **Stripe Payment Processing** (`backend/src/services/stripe.ts`)
- Payment Intent creation in XAF currency
- Payment confirmation and verification
- Refund processing
- Webhook event construction
- Secure transaction handling

#### 4. **Redis Caching** (`backend/src/services/redis.ts`)
- High-speed caching via Upstash Redis
- Cache invalidation strategies
- Caregiver profile caching
- Search results caching
- Reduces database load and improves performance

#### 5. **Email Service (Resend)** (`backend/src/services/resend.ts`)
- Welcome emails for new users
- Email verification
- Password reset flows
- Booking confirmations
- Verification approval/rejection notifications
- Message notifications

#### 6. **Analytics (PostHog)** (`backend/src/services/posthog.ts`)
- User signup and login tracking
- Search event tracking
- Booking lifecycle tracking
- Payment event tracking
- Admin action tracking
- Custom event capture

### Backend Routes (Fully Implemented)

#### 1. **Authentication Routes** (`backend/src/routes/auth.ts`)
- `POST /api/auth/sign-up` - User registration with role selection
- `POST /api/auth/login` - Authentication with JWT token generation
- `POST /api/auth/forgot-password` - Password reset request
- `POST /api/auth/reset-password` - Password update
- `GET /api/auth/verify-token` - Token validation and user retrieval

#### 2. **Caregiver Routes** (`backend/src/routes/caregivers.ts`)
- `GET /api/caregivers/:userId` - Fetch caregiver profile (cached)
- `PUT /api/caregivers/:userId` - Update profile and re-index in Pinecone
- `POST /api/caregivers/:userId/documents` - Submit verification documents
- `POST /api/caregivers/:userId/submit-verification` - Request verification
- `GET /api/caregivers` - List all verified caregivers with filters
- `POST /api/caregivers/search/semantic` - AI-powered semantic search

#### 3. **Booking Routes** (`backend/src/routes/bookings.ts`)
- `POST /api/bookings` - Create new booking
- `POST /api/bookings/:bookingId/payment` - Create Stripe payment intent
- `POST /api/bookings/:bookingId/confirm-payment` - Confirm payment and send emails
- `GET /api/bookings/user/:userId` - Get user's bookings (cached)
- `GET /api/bookings/:bookingId` - Get booking details
- `PUT /api/bookings/:bookingId` - Update booking status

#### 4. **Admin Routes** (`backend/src/routes/admin.ts`)
- `GET /api/admin/verification/pending` - Get pending verifications
- `GET /api/admin/verification/stats` - Verification statistics dashboard
- `POST /api/admin/verification/:caregiverId/approve` - Approve caregiver
- `POST /api/admin/verification/:caregiverId/reject` - Reject with reason
- `GET /api/admin/verification` - List all caregivers with filters
- `GET /api/admin/verification/:caregiverId/verification` - Verification details

#### 5. **Messaging Routes** (`backend/src/routes/messaging.ts`)
- `POST /api/messages/conversations` - Create or get conversation
- `GET /api/messages/user/:userId` - Get user conversations (cached)
- `GET /api/messages/conversation/:conversationId/messages` - Get messages
- `POST /api/messages/conversation/:conversationId/messages` - Send message with email notification
- `PUT /api/messages/conversation/:conversationId/read` - Mark as read
- `DELETE /api/messages/conversation/:conversationId` - Delete conversation

### Frontend Components (Partially Implemented)

#### 1. **Authentication Context** (`src/context/AuthContext.tsx`)
- ✅ Complete JWT-based authentication
- ✅ Token persistence in localStorage
- ✅ User state management
- ✅ PostHog integration for analytics
- ✅ Login/Signup/Logout methods
- ✅ Token verification

#### 2. **Authentication Pages** 
- ✅ **Login.tsx** - Beautiful login form with error handling
- ✅ **Signup.tsx** - Multi-step registration:
  - Step 1: Email & password
  - Step 2: ID verification documents
  - Step 3: Caregiver profile (if applicable)

#### 3. **UI Components**
- ✅ **ErrorMessage.tsx** - Consistent error display
- ✅ **LoadingSpinner.tsx** - Loading state indicator

### Database & Seed Data

#### 1. **Prisma Schema** (`backend/prisma/schema.prisma`)
- User model with role-based access
- Caregiver model with verification status
- Booking model with payment tracking
- Document storage for verification
- Conversation & Message models for messaging
- Verification audit trail

#### 2. **Seed Data** (`backend/src/seed.ts`)
- 1 Admin user
- 2 Family users (testing families)
- 5 Verified caregiver users with complete profiles
- Sample bookings (confirmed and pending)
- Sample conversations and messages
- Test credentials for development

## 🚀 Quick Start

### Backend Setup
```bash
cd backend

# Install dependencies
npm install

# Generate Prisma client
npm run prisma:generate

# Run seed script
npm run seed

# Start development server
npm run dev
```

### Frontend Setup
```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Environment variables needed in .env file
VITE_API_URL=http://localhost:4000
VITE_SENTRY_DSN=your-sentry-dsn
VITE_POSTHOG_KEY=your-posthog-key
```

### Environment Variables

#### Backend (.env)
```
DATABASE_URL=file:./dev.db
DIRECT_URL=file:./dev.db
SUPABASE_URL=your-supabase-url
SUPABASE_SERVICE_KEY=your-service-key
STRIPE_SECRET_KEY=your-stripe-secret
STRIPE_WEBHOOK_SECRET=your-webhook-secret
PINECONE_API_KEY=your-pinecone-key
PINECONE_INDEX_NAME=tataconnect
UPSTASH_REDIS_REST_URL=your-upstash-url
UPSTASH_REDIS_REST_TOKEN=your-upstash-token
RESEND_API_KEY=your-resend-key
FROM_EMAIL=noreply@tataconnect.cm
POSTHOG_API_KEY=your-posthog-key
POSTHOG_HOST=https://app.posthog.com
SENTRY_DSN=your-sentry-dsn
JWT_SECRET=dev-secret-key
FRONTEND_URL=http://localhost:5173
PORT=4000
OPENAI_API_KEY=your-openai-key
```

#### Frontend (.env)
```
VITE_API_URL=http://localhost:4000
VITE_SENTRY_DSN=your-sentry-dsn
VITE_POSTHOG_KEY=your-posthog-key
VITE_STRIPE_PUBLISHABLE_KEY=your-stripe-publishable-key
```

## 📋 Test Credentials

```
Admin Account:
  Email: admin@tataconnect.cm
  Password: admin123
  
Family Account:
  Email: family1@example.com
  Password: family123
  
Caregiver Accounts:
  marie@example.com / caregiver123
  jean@example.com / caregiver123
  alice@example.com / caregiver123
  sophie@example.com / caregiver123
  lucas@example.com / caregiver123
```

## ⚙️ Integration Checklist

### Stripe Setup (REQUIRED)
- [ ] Create Stripe account
- [ ] Enable XAF currency support
- [ ] Get Secret and Publishable keys
- [ ] Configure webhook endpoint: `{YOUR_BACKEND_URL}/api/bookings/webhook`
- [ ] Test in Stripe Dashboard

### Pinecone Setup (REQUIRED)
- [ ] Create Pinecone account
- [ ] Create index with dimension 1536
- [ ] Get API key
- [ ] Initialize index: `npm run prisma:generate` (in backend)

### Upstash Redis Setup (REQUIRED)
- [ ] Create Upstash Redis database
- [ ] Copy REST URL and Token
- [ ] Test connection

### Resend Email Setup (REQUIRED)
- [ ] Create Resend account
- [ ] Verify domain or use default email
- [ ] Get API key
- [ ] Test email sending

### Sentry Setup (OPTIONAL)
- [ ] Create Sentry account
- [ ] Create projects for frontend and backend
- [ ] Add DSN URLs to environment

### PostHog Setup (OPTIONAL)
- [ ] Create PostHog account
- [ ] Create projects for frontend and backend
- [ ] Add API keys to environment

## 📝 API Examples

### Login
```bash
curl -X POST http://localhost:4000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "family1@example.com",
    "password": "family123"
  }'
```

### Semantic Search Caregivers
```bash
curl -X POST http://localhost:4000/api/caregivers/search/semantic \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "query": "nanny who speaks French and likes dogs",
    "city": "Yaoundé",
    "limit": 10
  }'
```

### Create Booking
```bash
curl -X POST http://localhost:4000/api/bookings \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "familyId": "family-id",
    "caregiverId": "caregiver-id",
    "title": "Weekly Childcare",
    "description": "Monday to Friday, 8am-5pm",
    "startDate": "2024-06-01T08:00:00Z",
    "location": "Yaoundé, Mendong",
    "rateFcfa": 125000
  }'
```

### Create Payment Intent
```bash
curl -X POST http://localhost:4000/api/bookings/booking-id/payment \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "amountXAF": 125000
  }'
```

## 🔄 Next Implementation Steps

### Priority 1 (Frontend Pages)
1. **SearchCaregivers.tsx** - Implement caregiver search with:
   - Semantic search input
   - Filters (city, price, services, languages)
   - Caregiver cards display
   - Results pagination

2. **CaregiverProfile.tsx** - Show detailed profile with:
   - Profile info and ratings
   - Reviews section
   - Availability calendar
   - "Book Now" button

3. **BookingFlow.tsx** - Multi-step booking with:
   - Job description and dates
   - Stripe payment integration
   - Confirmation and success page

### Priority 2 (Admin & Messaging)
1. **AdminDashboard.tsx** - Verification queue with:
   - Pending verification list
   - Document viewer
   - Approve/Reject actions
   - Statistics dashboard

2. **Messaging.tsx** - Real-time chat interface with:
   - Conversation list
   - Message display
   - Message input
   - Real-time updates (Supabase Realtime)

### Priority 3 (Enhancements)
1. Stripe webhook handling
2. Email template customization
3. File upload integration (Supabase Storage)
4. Real-time notifications
5. Mobile responsiveness
6. Accessibility improvements

## 📊 Database Schema Overview

### Users Table
- id: Primary key
- email: Unique identifier
- password: Bcrypt hashed
- role: FAMILY, CAREGIVER, or ADMIN
- NIC verification fields
- Video URL for verification

### Caregivers Table
- userId: Foreign key to User
- Profile info (name, city, bio)
- Rating and reviews
- Languages and services (JSON)
- Availability schedule (JSON)
- Verification status and dates

### Bookings Table
- familyId & caregiverId: Foreign keys
- Job details (title, description, dates)
- Rate in XAF
- Stripe payment tracking
- Status (PENDING, CONFIRMED, COMPLETED, CANCELLED)

### Messages & Conversations
- Conversation: Links two users
- Message: Content with sender/timestamp
- Timestamps for last read tracking

### Verification Audit
- VerificationRequest: Tracks caregiver submissions
- Verification: Audit trail of admin actions

## 🔐 Security Features Implemented

- ✅ Bcrypt password hashing
- ✅ JWT token-based authentication
- ✅ Role-based access control
- ✅ CORS protection
- ✅ XSS protection via React
- ✅ Helmet security headers (in server.ts)
- ✅ Stripe PCI compliance
- ✅ Secure document storage URLs

## 📈 Performance Features

- ✅ Redis caching for frequently accessed data
- ✅ Pinecone vector indexing for fast semantic search
- ✅ Database query optimization
- ✅ Frontend lazy loading (React Router)
- ✅ API response compression
- ✅ CDN-ready static assets

## 🐛 Known Limitations & TODOs

1. **OpenAI Embeddings**: Currently using mock vectors; integrate real OpenAI API
2. **File Uploads**: Document uploads need Supabase Storage integration
3. **Real-time Messaging**: Implement Supabase Realtime or WebSockets
4. **Notifications**: Add push notifications for bookings/messages
5. **Mobile App**: React Native version in future
6. **Internationalization**: Add French/Pidgin translations

## 📞 Support & Troubleshooting

### Common Issues

1. **"Failed to connect to database"**
   - Ensure SQLite file exists: `backend/dev.db`
   - Run: `npm run prisma:generate`

2. **"Stripe payment failed"**
   - Verify Stripe keys in .env
   - Check XAF support is enabled
   - Test webhook locally: `stripe listen`

3. **"Embeddings not working"**
   - Verify OpenAI API key
   - Check Pinecone index dimensions (should be 1536)
   - Run seed script to populate test data

## 📚 Additional Resources

- [Prisma Documentation](https://www.prisma.io/docs/)
- [Stripe Documentation](https://stripe.com/docs)
- [Pinecone Documentation](https://docs.pinecone.io/)
- [Supabase Documentation](https://supabase.com/docs)
- [React Documentation](https://react.dev)
- [Express Documentation](https://expressjs.com/)

---

**Last Updated**: May 2024
**Status**: Core implementation complete, ready for frontend expansion
