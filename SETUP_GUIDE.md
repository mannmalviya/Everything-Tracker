# Setup Guide - Everything-Tracker

## Prerequisites

Before starting, ensure you have installed:
- **Python 3.11+** (`python --version`)
- **Node.js 18+** and npm (`node --version`)
- **Git** (already have this)
- **Supabase account** (create at https://supabase.com)

## Backend Setup (FastAPI + Python)

### 1. Create Backend Directory Structure
```bash
mkdir -p backend/app/{models,routers,services,utils}
mkdir -p backend/tests
cd backend
```

### 2. Initialize Python Virtual Environment
```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Linux/Mac:
source venv/bin/activate
# On Windows:
# venv\Scripts\activate
```

### 3. Create requirements.txt
```txt
fastapi==0.104.1
uvicorn[standard]==0.24.0
supabase==2.0.3
python-dotenv==1.0.0
pydantic==2.5.0
pydantic-settings==2.1.0
python-jose[cryptography]==3.3.0
passlib[bcrypt]==1.7.4
python-multipart==0.0.6
```

### 4. Install Dependencies
```bash
pip install -r requirements.txt
```

### 5. Create Environment File
```bash
# Create .env file
touch .env
```

Add to `.env`:
```
SUPABASE_URL=your_supabase_project_url
SUPABASE_KEY=your_supabase_anon_key
SUPABASE_SERVICE_KEY=your_supabase_service_role_key
JWT_SECRET=your_random_secret_key_here
ENVIRONMENT=development
CORS_ORIGINS=http://localhost:5173,http://localhost:3000
```

### 6. Create Basic Backend Files
- `app/main.py` - FastAPI application entry point
- `app/config.py` - Configuration management
- `app/database.py` - Supabase client setup
- `app/dependencies.py` - Auth and other dependencies

### 7. Run Backend
```bash
uvicorn app.main:app --reload --port 8000
```

Backend will be available at: `http://localhost:8000`
API docs at: `http://localhost:8000/docs`

---

## Frontend Setup (React + TypeScript + Vite)

### 1. Create Frontend Project
```bash
cd /home/mann/Everything-Tracker
npm create vite@latest frontend -- --template react-ts
cd frontend
```

### 2. Install Dependencies
```bash
# Core dependencies
npm install

# Additional dependencies
npm install react-router-dom @tanstack/react-query axios
npm install @supabase/supabase-js
npm install date-fns
npm install recharts

# UI Framework (choose one)
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

# OR use Material-UI
# npm install @mui/material @emotion/react @emotion/styled
```

### 3. Create Environment File
```bash
# In frontend directory
touch .env
```

Add to `.env`:
```
VITE_API_URL=http://localhost:8000
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### 4. Configure Tailwind CSS (if using Tailwind)
Update `tailwind.config.js`:
```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Add to `src/index.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### 5. Set Up Project Structure
Create directories:
```bash
mkdir -p src/{components/{layout,activity,analytics,social,ui},pages,hooks,services,contexts,types,utils,styles}
```

### 6. Run Frontend
```bash
npm run dev
```

Frontend will be available at: `http://localhost:5173`

---

## Supabase Setup

### 1. Create Supabase Project
1. Go to https://supabase.com
2. Sign up / Log in
3. Create new project
4. Note down:
   - Project URL
   - Anon public key
   - Service role key (from Settings > API)

### 2. Set Up Database Tables
1. Go to SQL Editor in Supabase dashboard
2. Run the SQL commands from PROJECT_PLAN.md to create tables
3. Set up Row Level Security (RLS) policies

### 3. Configure Authentication
1. Go to Authentication > Settings
2. Enable Email provider
3. Optional: Enable OAuth providers (Google, GitHub, etc.)
4. Configure site URL and redirect URLs

---

## Quick Start (Automated)

Would you like me to:
1. ✅ Create the backend directory structure and files
2. ✅ Initialize the frontend with Vite
3. ✅ Create all necessary configuration files
4. ✅ Set up basic FastAPI routes and React components

Just say "yes" and I'll set everything up for you!

---

## Development Workflow

Once everything is set up:

1. **Terminal 1**: Run backend
   ```bash
   cd backend
   source venv/bin/activate  # On Linux/Mac
   uvicorn app.main:app --reload
   ```

2. **Terminal 2**: Run frontend
   ```bash
   cd frontend
   npm run dev
   ```

3. **Access**:
   - Frontend: http://localhost:5173
   - Backend API: http://localhost:8000
   - API Docs: http://localhost:8000/docs

---

## Next Steps After Setup

1. Set up Supabase database tables (run SQL from PROJECT_PLAN.md)
2. Implement authentication endpoints
3. Create user profile functionality
4. Build activity CRUD operations
5. Follow the phases in PROJECT_PLAN.md
