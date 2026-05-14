# TataConnect — Verified Caregiver Platform

A full-stack web application connecting verified housekeepers and nannies with families in Cameroon, built with React, TypeScript, Express, and modern cloud integrations.

## 🏗️ Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | React 19 + TypeScript + Vite + React Router |
| **Backend** | Express.js (Node.js) + TypeScript |
| **Database** | Supabase (PostgreSQL) + Prisma ORM |
| **Auth** | Supabase Authentication |
| **Email** | Resend |
| **Analytics** | PostHog |
| **Error Monitoring** | Sentry |
| **Caching** | Upstash Redis |
| **Vector Search** | Pinecone |
| **Deployment Ready** | Vercel (frontend) + Railway/Render (backend) |

## 📁 Project Structure

```
tataconnect/
├── src/                          # React frontend
│   ├── components/               # Reusable UI components
│   ├── context/                  # Auth context with Supabase
│   ├── pages/                    # Main pages (Dashboard, Messaging, etc.)
│   ├── services/                 # API & Supabase clients
│   ├── main.tsx                  # Sentry & PostHog initialization
│   └── index.css                 # Global styles
│
├── backend/                      # Express API
│   ├── src/
│   │   ├── routes/               # API endpoints (auth, caregivers, messaging, etc.)
│   │   ├── services/             # Service modules (Pinecone, Upstash, PostHog)
│   │   ├── server.ts             # Sentry initialization & app setup
│   │   └── seed.ts               # Database seeding
│   ├── prisma/
│   │   └── schema.prisma         # Database schema
│   └── .env.example              # Environment template
│
├── .env.example                  # Frontend environment template
├── package.json                  # Frontend dependencies
└── README.md
```

## 🚀 Quick Start

### Prerequisites
- Node.js 18+
- npm or yarn
- Git

### Installation

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/tataconnect.git
cd tataconnect

# Install frontend dependencies
npm install

# Install backend dependencies
cd backend && npm install && cd ..
```

### Environment Setup

Copy the example files and add your API keys:

```bash
# Frontend
cp .env.example .env.local

# Backend
cp backend/.env.example backend/.env
```

Fill in the required environment variables:

**Frontend (.env.local):**
```env
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
VITE_SENTRY_DSN=your-sentry-dsn
VITE_POSTHOG_KEY=your-posthog-key
VITE_POSTHOG_HOST=https://app.posthog.com
```

**Backend (.env):**
```env
PORT=4000
DATABASE_URL="file:./prisma/dev.db"
SENTRY_DSN=your-sentry-dsn
RESEND_API_KEY=your-resend-api-key
POSTHOG_API_KEY=your-posthog-api-key
UPSTASH_REDIS_REST_URL=your-upstash-url
UPSTASH_REDIS_REST_TOKEN=your-upstash-token
PINECONE_API_KEY=your-pinecone-key
```

### Development

**Terminal 1: Frontend**
```bash
npm run dev
```
Runs at `http://localhost:5173`

**Terminal 2: Backend**
```bash
cd backend && npm run dev
```
Runs at `http://localhost:4000`

### Build

```bash
# Frontend
npm run build

# Backend
cd backend && npm run build
```

## 🔑 Key Features

### Core Functionality
- ✅ **User Authentication** — Signup/Login with Supabase
- ✅ **Caregiver Search** — Browse and filter verified caregivers
- ✅ **Messaging** — Real-time messaging between families and caregivers
- ✅ **Bookings** — Schedule and manage caregiver bookings
- ✅ **Admin Dashboard** — Verify caregiver documents

### Integrated Services
- 📧 **Email** — Welcome emails via Resend on signup
- 📊 **Analytics** — Track user behavior with PostHog
- 🔍 **Error Tracking** — Capture errors with Sentry
- ⚡ **Caching** — Session & data caching with Upstash Redis
- 🧠 **Vector Search** — Semantic search for caregivers via Pinecone

## 📊 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/sign-up` | Create new user account |
| POST | `/api/auth/login` | Login with email/password |
| GET | `/api/caregivers` | List verified caregivers |
| GET | `/api/caregivers/:userId` | Get caregiver profile |
| POST | `/api/messages/conversation` | Create conversation |
| GET | `/api/messages/conversations` | Get user conversations |
| POST | `/api/bookings` | Create booking |
| GET | `/api/admin/verification/pending` | Get pending verifications |

## 🛠️ Database

**Local Development (SQLite):**
```bash
# Run Prisma migrations
cd backend && npx prisma db push

# Seed sample data
npm run seed
```

**Production (Supabase PostgreSQL):**
Update `DATABASE_URL` in `.env` to your Supabase connection string.

## 📈 Monitoring & Analytics

- **Sentry** — Error tracking for frontend & backend
- **PostHog** — User analytics and event tracking
- **Upstash** — Redis operations for caching
- **Pinecone** — Vector database queries for semantic search

## 🚢 Deployment

### Frontend (Vercel)
```bash
npm install -g vercel
vercel
```

### Backend (Railway/Render)
1. Connect GitHub repository
2. Set environment variables
3. Deploy from `backend/` directory

## 📝 Environment Variables Reference

See `.env.example` and `backend/.env.example` for complete list of required variables.

## 🤝 Contributing

1. Create a feature branch (`git checkout -b feature/your-feature`)
2. Commit changes (`git commit -am 'Add your feature'`)
3. Push to branch (`git push origin feature/your-feature`)
4. Open a Pull Request

## 📄 License

ISC

## 👥 Author

TataConnect Development Team — Cameroon, 2026

---

**Ready to get started?** Check out the [QUICKSTART.md](./QUICKSTART.md) guide for step-by-step setup instructions.
# tataconnect_1
