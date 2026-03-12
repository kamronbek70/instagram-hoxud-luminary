# ✦ Luminary — Full-Stack Social Media Platform

A production-ready Instagram-like social media app built with Next.js 14, TypeScript, Prisma, PostgreSQL, and Cloudinary.

**Platform starts at zero** — no fake users, no fake posts, no seed social data.  
Every like, comment, follow, message and notification is created by real user actions.

---

## Tech Stack

| Layer | Tech |
|-------|------|
| Frontend | Next.js 14 App Router, TypeScript, Tailwind CSS |
| Auth | Custom JWT (jose) + httpOnly cookies |
| Database | PostgreSQL + Prisma ORM |
| Media Upload | Cloudinary |
| Real-time | Polling (3s) for messages; SSE-ready |
| State | Zustand |
| Validation | Zod |

---

## Prerequisites

- Node.js 18+
- PostgreSQL database (local or [Neon](https://neon.tech) / [Supabase](https://supabase.com) free tier)
- Cloudinary account (free tier works great)

---

## Setup Instructions

### 1. Clone & Install

```bash
cd luminary-app
npm install
```

### 2. Environment Variables

```bash
cp .env.example .env
```

Edit `.env` with your values:

```env
# PostgreSQL connection string
DATABASE_URL="postgresql://USER:PASSWORD@localhost:5432/luminary_db"

# Auth secret — generate with: openssl rand -base64 32
JWT_SECRET="your-very-secret-jwt-key-at-least-32-chars"

# Cloudinary — get from https://cloudinary.com/console
CLOUDINARY_CLOUD_NAME="your-cloud-name"
CLOUDINARY_API_KEY="your-api-key"
CLOUDINARY_API_SECRET="your-api-secret"
NEXT_PUBLIC_CLOUDINARY_CLOUD_NAME="your-cloud-name"
NEXT_PUBLIC_CLOUDINARY_UPLOAD_PRESET="luminary_uploads"
```

### 3. Database Setup

```bash
# Generate Prisma client
npm run db:generate

# Run migrations (creates all tables)
npm run db:migrate
# When prompted, enter migration name: "init"
```

Or use `db push` for quick setup:
```bash
npm run db:push
```

### 4. Cloudinary Setup

1. Go to [cloudinary.com](https://cloudinary.com) → Settings → Upload
2. Create an **Upload Preset** named `luminary_uploads`
3. Set it to **Unsigned** mode
4. Copy your cloud name, API key, and API secret to `.env`

### 5. Run the App

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000)

You'll be redirected to `/auth/register` — create your first account!

---

## Database Schema

```
User           — id, email, username, password
Profile        — displayName, bio, avatarUrl, website, isPrivate
Session        — JWT session management
Post           — caption, location, userId
PostMedia      — url, type (IMAGE/VIDEO), order
Reel           — videoUrl, thumbnailUrl, caption
Story          — mediaUrl, expiresAt (24h auto-expire)
StoryView      — tracks who viewed each story
Comment        — text, userId, postId/reelId
Like           — userId, postId/reelId (unique constraint)
Save           — userId, postId/reelId (unique constraint)
Follow         — followerId, followingId (unique constraint)
Conversation   — between 2 users
ConversationParticipant — user membership + lastReadAt
Message        — text, mediaUrl, senderId, conversationId
Notification   — type (LIKE/COMMENT/FOLLOW/MENTION), read state
```

---

## Features

### ✅ Auth
- Register with username, email, password (hashed with bcrypt)
- Login with username or email
- JWT sessions in httpOnly cookies (30-day expiry)
- Protected routes

### ✅ Feed
- Shows posts from followed users (if any) or all posts
- Infinite scroll with cursor pagination
- Empty state with CTA to explore or create

### ✅ Posts
- Multi-image/video upload via Cloudinary
- Caption, location
- Like/unlike (real DB, optimistic UI)
- Save/unsave
- Comment, delete comment
- Delete own post
- Report (UI ready)
- Double-tap to like

### ✅ Stories
- Upload image/video stories
- 24-hour auto-expiry
- Story viewer with progress bars
- View tracking

### ✅ Reels
- Upload video reels
- Vertical scroll feed (snap scroll)
- Like/unlike

### ✅ Profile
- View any user's profile
- Follow/unfollow (updates counters in real-time)
- Posts grid, Reels tab, Saved tab (own only)
- Edit profile (name, bio, website, avatar)

### ✅ Messages
- Create conversations by searching users
- Real-time messages via 3-second polling
- Send text messages
- Conversation list

### ✅ Notifications
- Created on like, comment, follow
- Read/unread state
- Mark all as read

### ✅ Explore
- Grid of all real posts (sorted by popularity)
- User search with instant results

### ✅ Settings
- Edit profile with avatar upload
- Private account toggle
- Logout

---

## API Routes

| Method | Route | Description |
|--------|-------|-------------|
| POST | /api/auth/register | Create account |
| POST | /api/auth/login | Login |
| POST | /api/auth/logout | Logout |
| GET | /api/auth/me | Get current user |
| GET | /api/posts | Feed (paginated) |
| POST | /api/posts | Create post |
| GET | /api/posts/[id] | Post detail |
| DELETE | /api/posts/[id] | Delete post |
| POST | /api/posts/[id]/like | Toggle like |
| POST | /api/posts/[id]/save | Toggle save |
| GET | /api/posts/[id]/comments | Get comments |
| POST | /api/posts/[id]/comments | Add comment |
| DELETE | /api/posts/[id]/comments | Delete comment |
| GET | /api/users/[username] | Profile |
| GET | /api/users/[username]/posts | User's posts |
| POST | /api/users/[username]/follow | Toggle follow |
| GET | /api/users/[username]/follows | Followers/following |
| PATCH | /api/users/me/profile | Update profile |
| GET | /api/users/me/saved | Saved posts |
| GET | /api/stories | Active stories |
| POST | /api/stories | Create story |
| POST | /api/stories/[id]/view | Mark viewed |
| GET | /api/reels | All reels |
| POST | /api/reels | Create reel |
| POST | /api/reels/[id]/like | Toggle like |
| GET | /api/notifications | Get notifications |
| PATCH | /api/notifications | Mark read |
| GET | /api/conversations | All conversations |
| POST | /api/conversations | Create/get conversation |
| GET | /api/conversations/[id]/messages | Get messages |
| POST | /api/conversations/[id]/messages | Send message |
| GET | /api/explore | Explore grid |
| GET | /api/search | Search users |
| POST | /api/upload | Upload media |

---

## Important Notes

- **No seed data** — platform starts completely empty
- **No fake activity** — all numbers are 0 until real users create content
- First user to register will see an empty feed (as expected)
- Stories expire after 24 hours automatically (checked via `expiresAt` filter)
- Messages use 3-second polling; replace with Socket.io for production

---

## Production Deployment

1. Set up a managed PostgreSQL (Neon, Supabase, Railway, etc.)
2. Deploy to Vercel: `npx vercel`
3. Add all env vars in Vercel dashboard
4. Run `prisma migrate deploy` against production DB

```bash
# Build check
npm run build
```
