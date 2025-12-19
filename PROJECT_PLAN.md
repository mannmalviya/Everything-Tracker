# Everything-Tracker - Project Plan

## Overview
A full-stack activity tracking application with social features, analytics, and GitHub-style visualizations.

## Tech Stack

### Backend
- **Framework**: Python 3.11+ with FastAPI
- **Database**: Supabase (PostgreSQL)
- **Authentication**: Supabase Auth (JWT + OAuth)
- **ORM**: SQLAlchemy or Supabase Python client
- **API Documentation**: FastAPI automatic OpenAPI/Swagger

### Frontend
- **Framework**: React 18+ with TypeScript
- **Build Tool**: Vite
- **Routing**: React Router
- **State Management**: React Query (TanStack Query) for server state
- **UI Components**: Tailwind CSS + shadcn/ui or Material-UI
- **Charts/Visualizations**: Recharts or Victory
- **Date Handling**: date-fns or Day.js

### Infrastructure
- **Database & Auth**: Supabase
- **Backend Hosting**: Railway, Render, or Fly.io
- **Frontend Hosting**: Vercel or Netlify
- **Real-time**: Supabase real-time subscriptions

---

## Database Schema

### Tables

#### 1. `users` (handled by Supabase Auth, extended with profiles)
```sql
-- Supabase auth.users table is automatically created
-- We'll create a public.profiles table for additional user data
```

#### 2. `profiles`
```sql
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id) ON DELETE CASCADE,
  username VARCHAR(50) UNIQUE NOT NULL,
  display_name VARCHAR(100),
  bio TEXT,
  avatar_url TEXT,
  is_public BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

#### 3. `activities`
```sql
CREATE TABLE activities (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  name VARCHAR(100) NOT NULL,
  description TEXT,
  unit_name VARCHAR(50), -- e.g., "miles", "pages", "hours"
  icon VARCHAR(50), -- emoji or icon identifier
  color VARCHAR(7), -- hex color for visualization
  is_public BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, name)
);
```

#### 4. `activity_entries`
```sql
CREATE TABLE activity_entries (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  activity_id UUID NOT NULL REFERENCES activities(id) ON DELETE CASCADE,
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  entry_date DATE NOT NULL,
  entry_time TIME,
  duration_minutes INTEGER, -- duration in minutes
  quantity DECIMAL(10, 2), -- custom metric value
  notes TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_activity_entries_activity_id ON activity_entries(activity_id);
CREATE INDEX idx_activity_entries_user_id ON activity_entries(user_id);
CREATE INDEX idx_activity_entries_date ON activity_entries(entry_date);
```

#### 5. `follows`
```sql
CREATE TABLE follows (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  follower_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  following_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(follower_id, following_id),
  CHECK (follower_id != following_id)
);

CREATE INDEX idx_follows_follower ON follows(follower_id);
CREATE INDEX idx_follows_following ON follows(following_id);
```

#### 6. `activity_likes` (optional - for social engagement)
```sql
CREATE TABLE activity_likes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  entry_id UUID NOT NULL REFERENCES activity_entries(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, entry_id)
);
```

---

## Backend API Structure

### Project Structure
```
backend/
├── app/
│   ├── __init__.py
│   ├── main.py              # FastAPI app initialization
│   ├── config.py            # Configuration and environment variables
│   ├── database.py          # Supabase client setup
│   ├── dependencies.py      # Dependency injection (auth, etc.)
│   ├── models/              # Pydantic models
│   │   ├── __init__.py
│   │   ├── user.py
│   │   ├── activity.py
│   │   └── entry.py
│   ├── routers/             # API route handlers
│   │   ├── __init__.py
│   │   ├── auth.py
│   │   ├── users.py
│   │   ├── activities.py
│   │   ├── entries.py
│   │   ├── social.py
│   │   └── analytics.py
│   ├── services/            # Business logic
│   │   ├── __init__.py
│   │   ├── auth_service.py
│   │   ├── activity_service.py
│   │   └── analytics_service.py
│   └── utils/
│       ├── __init__.py
│       └── helpers.py
├── tests/
├── requirements.txt
├── .env.example
└── README.md
```

### API Endpoints

#### Authentication (`/api/auth`)
- `POST /auth/signup` - Register new user
- `POST /auth/login` - Login user
- `POST /auth/logout` - Logout user
- `GET /auth/me` - Get current user
- `POST /auth/refresh` - Refresh token

#### Users (`/api/users`)
- `GET /users/me` - Get current user profile
- `PUT /users/me` - Update current user profile
- `GET /users/{username}` - Get user profile by username
- `GET /users/{user_id}/activities` - Get user's public activities
- `GET /users/{user_id}/stats` - Get user statistics

#### Activities (`/api/activities`)
- `GET /activities` - List current user's activities
- `POST /activities` - Create new activity
- `GET /activities/{activity_id}` - Get activity details
- `PUT /activities/{activity_id}` - Update activity
- `DELETE /activities/{activity_id}` - Delete activity
- `GET /activities/{activity_id}/stats` - Get activity statistics

#### Activity Entries (`/api/entries`)
- `GET /entries` - List entries (with filters: activity_id, date_range)
- `POST /entries` - Create new entry
- `GET /entries/{entry_id}` - Get entry details
- `PUT /entries/{entry_id}` - Update entry
- `DELETE /entries/{entry_id}` - Delete entry
- `GET /entries/calendar/{activity_id}` - Get calendar data for activity

#### Social (`/api/social`)
- `GET /social/feed` - Get activity feed from followed users
- `POST /social/follow/{user_id}` - Follow a user
- `DELETE /social/follow/{user_id}` - Unfollow a user
- `GET /social/followers` - Get current user's followers
- `GET /social/following` - Get users current user follows
- `GET /social/leaderboard` - Get global leaderboard (by activity type)
- `POST /social/like/{entry_id}` - Like an entry
- `DELETE /social/like/{entry_id}` - Unlike an entry

#### Analytics (`/api/analytics`)
- `GET /analytics/heatmap/{activity_id}` - GitHub-style contribution heatmap
- `GET /analytics/streaks/{activity_id}` - Get current/longest streaks
- `GET /analytics/trends/{activity_id}` - Get trends over time
- `GET /analytics/comparison` - Compare with followed users

---

## Frontend Structure

### Project Structure
```
frontend/
├── public/
├── src/
│   ├── main.tsx             # Entry point
│   ├── App.tsx              # Root component
│   ├── components/          # Reusable components
│   │   ├── layout/
│   │   │   ├── Navbar.tsx
│   │   │   ├── Sidebar.tsx
│   │   │   └── Footer.tsx
│   │   ├── activity/
│   │   │   ├── ActivityCard.tsx
│   │   │   ├── ActivityForm.tsx
│   │   │   ├── ActivityList.tsx
│   │   │   └── EntryForm.tsx
│   │   ├── analytics/
│   │   │   ├── HeatmapCalendar.tsx
│   │   │   ├── StreakCounter.tsx
│   │   │   ├── TrendChart.tsx
│   │   │   └── StatsCard.tsx
│   │   ├── social/
│   │   │   ├── UserCard.tsx
│   │   │   ├── FeedItem.tsx
│   │   │   ├── Leaderboard.tsx
│   │   │   └── FollowButton.tsx
│   │   └── ui/              # Base UI components
│   │       ├── Button.tsx
│   │       ├── Input.tsx
│   │       ├── Modal.tsx
│   │       └── Card.tsx
│   ├── pages/               # Page components
│   │   ├── Home.tsx
│   │   ├── Login.tsx
│   │   ├── Signup.tsx
│   │   ├── Dashboard.tsx
│   │   ├── ActivityDetail.tsx
│   │   ├── Profile.tsx
│   │   ├── UserProfile.tsx  # View other users
│   │   ├── Feed.tsx
│   │   └── Leaderboard.tsx
│   ├── hooks/               # Custom React hooks
│   │   ├── useAuth.ts
│   │   ├── useActivities.ts
│   │   ├── useEntries.ts
│   │   └── useSocial.ts
│   ├── services/            # API service layer
│   │   ├── api.ts           # Axios instance
│   │   ├── auth.ts
│   │   ├── activities.ts
│   │   ├── entries.ts
│   │   └── social.ts
│   ├── contexts/            # React contexts
│   │   └── AuthContext.tsx
│   ├── types/               # TypeScript types
│   │   └── index.ts
│   ├── utils/
│   │   └── helpers.ts
│   └── styles/
│       └── globals.css
├── package.json
├── tsconfig.json
├── vite.config.ts
└── tailwind.config.js
```

### Key Pages/Views

1. **Landing Page** (`/`)
   - Hero section
   - Features showcase
   - Call to action (Sign up)

2. **Dashboard** (`/dashboard`)
   - Overview of all user's activities
   - Quick add entry button
   - Recent entries
   - Summary statistics

3. **Activity Detail** (`/activities/:id`)
   - GitHub-style heatmap calendar
   - Streak counter
   - Trend charts
   - Entry list/timeline
   - Add entry form

4. **Feed** (`/feed`)
   - Activity feed from followed users
   - Like/comment on entries
   - Filter by activity type

5. **Leaderboard** (`/leaderboard`)
   - Global rankings
   - Filter by activity type
   - Filter by time period (week/month/year)

6. **Profile** (`/profile`)
   - Edit user profile
   - Privacy settings
   - Activity management

7. **User Profile** (`/users/:username`)
   - View other user's public activities
   - Follow/unfollow button
   - Public statistics

---

## Key Features Implementation

### 1. GitHub-Style Heatmap Calendar
- Display full year of activity
- Color intensity based on quantity/frequency
- Tooltips showing details on hover
- Libraries: `react-calendar-heatmap` or custom with D3.js

### 2. Streak Tracking
- Calculate current streak (consecutive days)
- Track longest streak
- Visual streak counter
- Streak freeze/protection feature (optional)

### 3. Activity Feed
- Real-time updates using Supabase subscriptions
- Infinite scroll pagination
- Filter by followed users
- Show activity type, user, timestamp

### 4. Leaderboard System
- Rank users by total entries, total quantity, or streaks
- Filter by time period and activity type
- Show user's rank
- Pagination

### 5. Privacy Controls
- Activity-level privacy (public/private)
- Profile-level privacy
- Control who can see your data

---

## Implementation Phases

### Phase 1: Foundation (Week 1-2)
1. Set up project structure
   - Initialize backend (FastAPI + Poetry/pip)
   - Initialize frontend (Vite + React + TypeScript)
   - Set up Supabase project
2. Configure Supabase
   - Create database tables
   - Set up Row Level Security (RLS) policies
   - Configure authentication
3. Basic authentication
   - Backend auth middleware
   - Frontend auth context
   - Login/signup pages
4. User profiles
   - Create/update profile
   - Profile page

### Phase 2: Core Activity Tracking (Week 3-4)
1. Activity CRUD operations
   - Backend API endpoints
   - Frontend forms and list views
2. Entry CRUD operations
   - Create/edit/delete entries
   - Entry list with filters
3. Basic dashboard
   - Activity list
   - Recent entries
   - Quick stats

### Phase 3: Analytics & Visualizations (Week 5-6)
1. Heatmap calendar
   - Backend endpoint for calendar data
   - Frontend heatmap component
2. Streak calculation
   - Backend streak logic
   - Frontend streak display
3. Charts and trends
   - Time-series charts
   - Statistics cards
4. Activity detail page
   - Comprehensive analytics view

### Phase 4: Social Features (Week 7-8)
1. Follow system
   - Backend follow/unfollow endpoints
   - Frontend follow buttons
   - Followers/following lists
2. Activity feed
   - Backend feed generation
   - Frontend feed component
   - Real-time updates
3. Leaderboard
   - Backend ranking logic
   - Frontend leaderboard page
   - Filters and pagination
4. User discovery
   - Search users
   - Suggested users to follow

### Phase 5: Polish & Additional Features (Week 9-10)
1. Notifications
   - Email notifications (optional)
   - In-app notifications
2. Mobile responsiveness
   - Optimize all views for mobile
3. Performance optimization
   - Database query optimization
   - Frontend code splitting
   - Caching strategies
4. Testing
   - Backend unit tests
   - Frontend component tests
   - E2E tests (Playwright/Cypress)

---

## Environment Variables

### Backend `.env`
```
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_anon_key
SUPABASE_SERVICE_KEY=your_supabase_service_key
JWT_SECRET=your_jwt_secret
ENVIRONMENT=development
CORS_ORIGINS=http://localhost:5173
```

### Frontend `.env`
```
VITE_API_URL=http://localhost:8000
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

---

## Supabase Row Level Security (RLS) Policies

### Profiles Table
```sql
-- Enable RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Users can read public profiles
CREATE POLICY "Public profiles are viewable by everyone"
ON profiles FOR SELECT
USING (is_public = true OR auth.uid() = id);

-- Users can update their own profile
CREATE POLICY "Users can update own profile"
ON profiles FOR UPDATE
USING (auth.uid() = id);
```

### Activities Table
```sql
ALTER TABLE activities ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view their own activities"
ON activities FOR SELECT
USING (auth.uid() = user_id);

CREATE POLICY "Users can view public activities"
ON activities FOR SELECT
USING (is_public = true);

CREATE POLICY "Users can create their own activities"
ON activities FOR INSERT
WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can update their own activities"
ON activities FOR UPDATE
USING (auth.uid() = user_id);

CREATE POLICY "Users can delete their own activities"
ON activities FOR DELETE
USING (auth.uid() = user_id);
```

### Activity Entries Table
```sql
ALTER TABLE activity_entries ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can manage their own entries"
ON activity_entries FOR ALL
USING (auth.uid() = user_id);

CREATE POLICY "Public entries are viewable"
ON activity_entries FOR SELECT
USING (
  EXISTS (
    SELECT 1 FROM activities
    WHERE activities.id = activity_entries.activity_id
    AND activities.is_public = true
  )
);
```

### Follows Table
```sql
ALTER TABLE follows ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view their own follows"
ON follows FOR SELECT
USING (auth.uid() = follower_id OR auth.uid() = following_id);

CREATE POLICY "Users can create their own follows"
ON follows FOR INSERT
WITH CHECK (auth.uid() = follower_id);

CREATE POLICY "Users can delete their own follows"
ON follows FOR DELETE
USING (auth.uid() = follower_id);
```

---

## Next Steps

1. **Review this plan** and provide feedback on any changes needed
2. **Set up Supabase project** - Create account and new project
3. **Start with Phase 1** - Set up project structure and basic auth
4. **Iterate and build** - Follow the phases or customize as needed

---

## Optional Enhancements for Future

- **Goals & Milestones**: Set and track activity goals
- **Challenges**: Create/join community challenges
- **Achievements/Badges**: Gamification elements
- **Data Export**: Export activity data to CSV/JSON
- **API Integrations**: Connect with Strava, Fitbit, etc.
- **Teams/Groups**: Collaborative activity tracking
- **Mobile App**: React Native version
- **AI Insights**: ML-powered insights and predictions
- **Custom Themes**: Dark mode, custom color schemes
