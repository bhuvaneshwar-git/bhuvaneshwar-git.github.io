---
title: college project documentation
date: 2026-02-07
categories: [project]
---

# Linux OS in Browser - Complete Documentation

## Project Overview

A web application that allows users to run Linux operating systems (Kali Linux and Parrot) directly in their browser through VNC. The application features user authentication via Authgear, session management with login/logout tracking, and isolated Docker containers for each user.

### Key Features

- **User Authentication**: Secure login via Authgear OAuth 2.0
- **Multi-OS Support**: Run Kali Linux or Ubuntu in browser
- **Session Tracking**: Login/logout timestamps stored in SQLite database
- **User Isolation**: Each user gets their own Docker container with persistent data
- **VNC Access**: Browser-based VNC client (noVNC) for GUI access
- **Session Management**: View login history, active sessions, reconnect to running containers

---

## Tech Stack

### Frontend
- **React** (Vite) - UI framework
- **React Router** - Client-side routing
- **Authgear Web SDK** - Authentication
- **Tailwind CSS** - Styling

### Backend
- **Node.js** with Express - API server
- **SQLite** (sql.js) - Lightweight database for session tracking
- **JWT** - Token validation
- **Docker** - Container management

### Infrastructure
- **Authgear** - Authentication provider
- **Docker** - Container runtime
- **noVNC** - Browser-based VNC client

---

## Prerequisites

Before starting, ensure you have the following installed:

### Required Software

1. **Node.js** (v18 or higher)
   ```bash
   node --version  # Should be v18+
   ```

2. **Docker** (for running Linux containers)
   ```bash
   docker --version
   ```

3. **Git** (for cloning repository)
   ```bash
   git --version
   ```

### System Requirements

- **RAM**: Minimum 4GB (8GB recommended)
- **Storage**: 10GB free space
- **OS**: Linux, macOS, or Windows with WSL2
- **Processor**: x86_64 architecture

---

## Installation Guide

### Step 1: Clone the Repository

```bash
git clone https://github.com/bhuvaneshwar-git/college_project.git
cd college_project
```

### Step 2: Project Structure

Your project should have this structure:

```
project-root/
├── persistence_storage/
├── public/
├── screenshots/
├── vnc-frontend/
├── vnc_kali/
├── vnc_parrot/
│
├── windows-server.js/
│   └── server.js
│
├── authgear.db
├── package.json
├── package-lock.json
├── README.md
├── server.js
├── vnc_kali/
│   ├── Dockerfile
│   ├── start-vnc.sh
│   ├── vnc-control-panel.js
│   └── vnc-control-styles.css
│
└── vnc_parrot/
    ├── Dockerfile
    ├── start-vnc.sh
    ├── vnc-control-panel.js
    └── vnc-control-styles.css

```

---

## Setup Instructions

### Part A: Authgear Configuration

#### 1. Create Authgear Account

1. Go to [Authgear Portal](https://portal.authgear.com)
2. Sign up for a free account
3. Create a new project

#### 2. Configure Application

1. In Authgear Portal, click **"Applications"**
2. Click **"Add Application"**
3. Select **"Single Page Application (SPA)"**
4. Configure settings:
   - **Application Name**: "Linux OS Browser"
   - **Application Type**: Single Page Application
   - **Authorized Redirect URIs**: 
     ```
     http://localhost:5173/login
     ```
   - **Post Logout Redirect URIs**:
     ```
     http://localhost:5173/login
     ```

5. **Save** the configuration

#### 3. Get Your Credentials

From the application settings, copy:
- **Client ID** (e.g., `abcd1234efgh5678`)
- **Endpoint** (e.g., `https://your-app.authgear.cloud`)

#### 4. Configure User Profile (Optional)

1. Go to **User Management**
2. Click on your user account
3. Set **"Name"** or **"Given Name"** to your preferred display name
4. Save changes

---

### Part B: Backend Setup

#### 1. Navigate to Backend Directory

```bash
cd backend
```

#### 2. Install Dependencies

```bash
npm install
```

Required packages:
```bash
npm install express cors jsonwebtoken sql.js
```

#### 3. Create Environment File

Create `backend/.env`:

```env
# Authgear Configuration
AUTHGEAR_ENDPOINT=https://your-app.authgear.cloud
AUTHGEAR_CLIENT_ID=your_client_id_here

# Server Configuration
PORT=3001

# User Data Directory
USER_DATA_DIR=/home/ubuntu/user_data
```

**Replace:**
- `your-app.authgear.cloud` with your actual Authgear endpoint
- `your_client_id_here` with your actual Client ID

#### 4. Verify server.js

Ensure `server.js` exists with all required endpoints:
- `/api/login` - Record login
- `/api/logout` - Record logout
- `/api/sessions` - Get session history
- `/start-container` - Start Docker container
- `/stop-container` - Stop Docker container
- `/session-info` - Get active sessions

---

### Part C: Frontend Setup

#### 1. Navigate to Frontend Directory

```bash
cd ../frontend
# or if you're in root: cd frontend
```

#### 2. Install Dependencies

```bash
npm install
```

Required packages:
```bash
npm install @authgear/web react-router-dom
```

#### 3. Create Environment File

Create `frontend/.env`:

```env
VITE_AUTHGEAR_ENDPOINT=https://your-app.authgear.cloud
VITE_AUTHGEAR_CLIENT_ID=your_client_id_here
```

**Important:** 
- Use `VITE_` prefix for Vite environment variables
- Use the SAME values as backend `.env`

#### 4. Verify File Structure

Ensure these files exist:
```
src/
├── components/
│   ├── AuthProvider.jsx      ✓ Authentication context
│   └── ProtectedRoute.jsx    ✓ Route guard
├── App.jsx                    ✓ Main application
├── Login.jsx                  ✓ Login page
└── main.jsx                   ✓ App entry point
```

---

### Part D: Docker Setup

#### 1. Build VNC Docker Images

You need Docker images for Ubuntu and Kali Linux with VNC.

**Create Dockerfile for Ubuntu:**

```dockerfile
# Dockerfile.ubuntu
FROM dorowu/ubuntu-desktop-lxde-vnc:latest

# Set resolution
ENV RESOLUTION=1400x600

# Create student directory
RUN mkdir -p /root/Desktop/student

# Expose VNC and noVNC ports
EXPOSE 5901 6081

CMD ["/startup.sh"]
```

**Build Ubuntu Image:**
```bash
docker build -f Dockerfile.ubuntu -t vnc_ubuntu .
```

**Create Dockerfile for Kali:**

```dockerfile
# Dockerfile.kali
FROM kalilinux/kali-rolling

# Install VNC and desktop
RUN apt-get update && apt-get install -y \
    xfce4 xfce4-goodies \
    tightvncserver \
    novnc \
    websockify \
    && apt-get clean

# Set VNC password
RUN mkdir /root/.vnc && \
    echo "password" | vncpasswd -f > /root/.vnc/passwd && \
    chmod 600 /root/.vnc/passwd

# Create student directory
RUN mkdir -p /root/Desktop/student

# Expose ports
EXPOSE 5901 6080

# Start script
COPY start-kali.sh /start.sh
RUN chmod +x /start.sh

CMD ["/start.sh"]
```

**Create start script (`start-kali.sh`):**

```bash
#!/bin/bash
vncserver :1 -geometry 1400x600 -depth 24
websockify --web=/usr/share/novnc 6080 localhost:5901
```

**Build Kali Image:**
```bash
docker build -f Dockerfile.kali -t vnc_kali .
```

#### 2. Verify Images

```bash
docker images
```

You should see:
```
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
vnc_parrot    latest    xxxxxxxxxxxx   X minutes ago   XXX MB
vnc_kali      latest    xxxxxxxxxxxx   X minutes ago   XXX MB
```

---

## Running the Application

### Step 1: Start Backend Server

```bash
cd backend
node server.js
```

**Expected Output:**
```
✅ SQLite Database initialized
🚀 Multi-user VNC server running at http://localhost:3001
📁 User data stored in: /home/ubuntu/user_data
🌐 CORS enabled for: http://localhost:5173
```

**Keep this terminal running!**

---

### Step 2: Start Frontend Development Server

Open a **new terminal**:

```bash
cd frontend
npm run dev
```

**Expected Output:**
```
VITE v7.3.1  ready in 1054 ms

➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
➜  press h + enter to show help
```

---

### Step 3: Access the Application

1. Open browser and go to: **http://localhost:5173/**
2. You'll be redirected to: **http://localhost:5173/login**
3. Click **"LOGIN WITH AUTHGEAR"**
4. Enter your Authgear credentials
5. After authentication, you'll see the main application

---

## Application Usage

### 1. Login

- Navigate to `http://localhost:5173/`
- Click "LOGIN WITH AUTHGEAR" button
- Enter your email and password
- After successful login, you'll see your username displayed

### 2. Start a Container

- Choose either **Ubuntu** or **Kali Linux**
- Click **"START [OS]"** button
- Wait for container initialization (15 seconds)
- Browser will automatically redirect to noVNC viewer
- Access your Linux desktop in the browser!

### 3. View Session History

- Click **"SHOW LOGS"** button
- View all your login/logout timestamps
- See session duration
- Check active vs. ended sessions

### 4. Reconnect to Container

If you have an active container:
- **"RECONNECT"** button appears instead of "START"
- Click to reopen your existing session
- Your data is preserved

### 5. Stop Container

- Click **"STOP"** button
- Confirm the action
- Container is stopped and removed
- Your data in `/student` folder is preserved

### 6. Logout

- Click **"LOGOUT"** button
- Logout timestamp is recorded
- Redirected back to login page

---

## Database Structure

### SQLite Database (`authgear.db`)

**Table: `user_sessions`**

| Column      | Type    | Description                    |
|-------------|---------|--------------------------------|
| id          | INTEGER | Primary key (auto-increment)   |
| userId      | TEXT    | Authgear user ID (UUID)        |
| username    | TEXT    | Display name from Authgear     |
| email       | TEXT    | User email address             |
| loginTime   | TEXT    | ISO timestamp of login         |
| logoutTime  | TEXT    | ISO timestamp of logout (null if active) |
| isActive    | INTEGER | 1 = active session, 0 = ended  |

**Example Data:**

```sql
sqlite3 backend/authgear.db

SELECT * FROM user_sessions;
```

Output:
```
id|userId|username|email|loginTime|logoutTime|isActive
1|db914422-3815-4f84-b8a1-fd32401c3914|Bhuvaneshwar|user@example.com|2026-01-13T10:44:00.000Z|2026-01-13T10:46:00.000Z|0
2|db914422-3815-4f84-b8a1-fd32401c3914|Bhuvaneshwar|user@example.com|2026-01-13T11:17:00.000Z|NULL|1
```

---

## Architecture

### Authentication Flow

```
1. User visits http://localhost:5173/
   ↓
2. Not authenticated → Redirect to /login
   ↓
3. User clicks "LOGIN WITH AUTHGEAR"
   ↓
4. Redirect to Authgear (https://your-app.authgear.cloud)
   ↓
5. User enters credentials
   ↓
6. Authgear validates and generates authorization code
   ↓
7. Redirect back to http://localhost:5173/login?code=xxx
   ↓
8. AuthProvider exchanges code for access token
   ↓
9. Fetch user info from Authgear
   ↓
10. Record login in SQLite database
   ↓
11. Redirect to / (main app)
   ↓
12. User sees Linux OS selector
```

### Container Management Flow

```
1. User clicks "START Kali"
   ↓
2. Frontend sends POST to /start-container?os=kali
   ↓
3. Backend validates JWT token
   ↓
4. Backend checks for existing container
   ↓
5. If exists → Return reconnect info
   ↓
6. If not → Create new Docker container
   ├── Allocate unique ports (VNC + noVNC)
   ├── Create user data directory
   ├── Mount volume for persistence
   └── Start container
   ↓
7. Wait for container to be ready
   ↓
8. Return noVNC URL to frontend
   ↓
9. Frontend redirects to noVNC viewer
   ↓
10. User accesses Linux desktop in browser
```

### Session Tracking Flow

```
Login:
1. User authenticates with Authgear
2. AuthProvider.jsx records login
3. POST /api/login with username, email, timestamp
4. Backend stores in SQLite
5. Sets isActive = 1

Logout:
1. User clicks LOGOUT
2. AuthProvider.jsx records logout
3. POST /api/logout
4. Backend updates logoutTime
5. Sets isActive = 0
6. Clears React state
7. Redirects to /login
```

---

## API Endpoints

### Authentication Endpoints

#### POST `/api/login`
Record user login to database

**Headers:**
```
Authorization: Bearer <access_token>
Content-Type: application/json
```

**Body:**
```json
{
  "username": "Bhuvaneshwar",
  "email": "user@example.com"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login recorded",
  "sessionId": 1
}
```

---

#### POST `/api/logout`
Record user logout

**Headers:**
```
Authorization: Bearer <access_token>
```

**Response:**
```json
{
  "success": true,
  "message": "Logout recorded"
}
```

---

#### GET `/api/sessions`
Get user's session history

**Headers:**
```
Authorization: Bearer <access_token>
```

**Response:**
```json
{
  "success": true,
  "sessions": [
    {
      "_id": 1,
      "userId": "db914422-3815...",
      "username": "Bhuvaneshwar",
      "email": "user@example.com",
      "loginTime": "2026-01-13T11:17:00.000Z",
      "logoutTime": null,
      "isActive": true
    }
  ]
}
```

---

### Container Management Endpoints

#### POST `/start-container?os=<parrot|kali>`
Start a Docker container for user

**Headers:**
```
Authorization: Bearer <access_token>
```

**Response:**
```json
{
  "success": true,
  "url": "http://localhost:8001/vnc.html?autoconnect=true",
  "userId": "db914422-3815...",
  "containerName": "vnc_kali_db914422",
  "vncPort": 7042,
  "novncPort": 8042,
  "os": "kali",
  "message": "Container started successfully"
}
```

---

#### POST `/stop-container?os=<parrot|kali>`
Stop user's container

**Headers:**
```
Authorization: Bearer <access_token>
```

**Response:**
```json
{
  "success": true,
  "message": "Container stopped successfully"
}
```

---

#### GET `/session-info`
Get user's active containers

**Headers:**
```
Authorization: Bearer <access_token>
```

**Response:**
```json
{
  "success": true,
  "sessions": [
    {
      "os": "kali",
      "url": "http://localhost:8042/vnc.html",
      "containerName": "vnc_kali_db914422",
      "vncPort": 7042,
      "novncPort": 8042,
      "startedAt": "2026-01-13T11:20:00.000Z"
    }
  ],
  "hasSession": true
}
```

---

## Environment Variables

### Frontend (.env)

```env
# Authgear Configuration
VITE_AUTHGEAR_ENDPOINT=https://your-app.authgear.cloud
VITE_AUTHGEAR_CLIENT_ID=your_client_id

# Note: Use VITE_ prefix for Vite to expose variables
```

### Backend (.env)

```env
# Authgear Configuration
AUTHGEAR_ENDPOINT=https://your-app.authgear.cloud
AUTHGEAR_CLIENT_ID=your_client_id

# Server Configuration
PORT=3001

# Docker Configuration
USER_DATA_DIR=/home/ubuntu/user_data
BASE_PORT=7000
NOVNC_BASE_PORT=8000
```

---

## Troubleshooting

### Issue 1: "Cannot connect to backend"

**Symptoms:**
- Frontend shows login page but can't fetch sessions
- Console shows `ERR_CONNECTION_REFUSED`

**Solution:**
```bash
# Check if backend is running
ps aux | grep node

# Start backend if not running
cd backend
node server.js
```

---

### Issue 2: "Invalid authorization code"

**Symptoms:**
- Login works but shows "OAuthError2: invalid_grant"
- Multiple login entries created

**Solution:**
1. Clear browser cache (Ctrl+Shift+Delete)
2. Use Incognito mode
3. Verify redirect URI in Authgear Portal matches `http://localhost:5173/login`

---

### Issue 3: "Username shows 'User' instead of real name"

**Symptoms:**
- Authentication works but displays "User"

**Solution:**
1. Check Network tab → `userinfo` response
2. Verify `given_name` or `preferred_username` has a value
3. Update profile in Authgear Portal:
   - Go to User Management
   - Edit your user
   - Set "Name" or "Given Name"
   - Save

---

### Issue 4: "Docker container fails to start"

**Symptoms:**
- "Failed to start container" error
- No noVNC window opens

**Solution:**
```bash
# Check if Docker is running
docker ps

# Check if images exist
docker images

# Build missing images
docker build -f Dockerfile.ubuntu -t vnc_ubuntu .
docker build -f Dockerfile.kali -t vnc_kali .

# Check ports are available
lsof -i :7000-8999
```

---

### Issue 5: "Port already in use"

**Symptoms:**
- `EADDRINUSE` error when starting backend

**Solution:**
```bash
# Find process using port 3001
lsof -i :3001

# Kill the process
kill -9 <PID>

# Or change port in backend/.env
PORT=3002
```

---

### Issue 6: "JwksError: Not Found"

**Symptoms:**
- Backend logs show repeated `JwksError: Not Found`
- Login recording fails

**Solution:**
1. Verify `AUTHGEAR_ENDPOINT` in `backend/.env`
2. Should be: `https://your-app.authgear.cloud` (no trailing slash)
3. Restart backend after fixing

---

## Testing

### Manual Testing Checklist

- [ ] Navigate to `http://localhost:5173/`
- [ ] Redirects to `/login` page
- [ ] Click "LOGIN WITH AUTHGEAR"
- [ ] Redirects to Authgear login page
- [ ] Enter credentials
- [ ] Redirects back to app
- [ ] Username displayed correctly at top
- [ ] Click "SHOW LOGS" - see login timestamp
- [ ] Click "START UBUNTU" - container starts
- [ ] After 30 seconds - noVNC opens in browser
- [ ] Can interact with Ubuntu desktop
- [ ] Close noVNC window
- [ ] Click "RECONNECT" - returns to same session
- [ ] Click "STOP" - container stops
- [ ] Click "LOGOUT" - redirects to login
- [ ] Click "SHOW LOGS" - see logout timestamp

---

## Security Considerations

### Authentication
- ✅ JWT tokens used for API authentication
- ✅ Tokens validated on every request
- ✅ OAuth 2.0 Authorization Code flow
- ✅ Secure token storage (IndexedDB)

### Container Isolation
- ✅ Each user gets unique ports
- ✅ Separate data directories per user
- ✅ Containers isolated from host

### Improvements Needed
- ⚠️ Add HTTPS in production
- ⚠️ Implement rate limiting
- ⚠️ Add container resource limits (CPU/RAM)
- ⚠️ Implement session timeouts
- ⚠️ Add audit logging

---

## Deployment (Production)

### Prerequisites
- Domain name
- SSL certificate
- Production server (Linux)

### Steps

1. **Update Authgear**
   - Add production redirect URIs
   - Example: `https://yourdomain.com/login`

2. **Update Environment Variables**
   ```env
   # Frontend
   VITE_AUTHGEAR_ENDPOINT=https://your-app.authgear.cloud
   VITE_AUTHGEAR_CLIENT_ID=your_client_id

   # Backend
   PORT=3001
   NODE_ENV=production
   ```

3. **Build Frontend**
   ```bash
   cd frontend
   npm run build
   ```

4. **Deploy Backend**
   ```bash
   # Use PM2 for process management
   npm install -g pm2
   pm2 start server.js --name "linux-os-backend"
   ```

5. **Configure Nginx**
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com;

       location / {
           proxy_pass http://localhost:5173;
       }

       location /api {
           proxy_pass http://localhost:3001;
       }
   }
   ```

6. **Enable SSL**
   ```bash
   sudo certbot --nginx -d yourdomain.com
   ```

---


