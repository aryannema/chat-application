# MERN Chat App

A real-time chat application built with a React frontend and an Express/MongoDB backend. The project uses JWT cookie-based authentication, Socket.IO for live presence and messaging updates, and Cloudinary for profile image and message image uploads.

## Overview

This repository is split into two apps:

- `frontend/`: Vite + React client
- `backend/`: Express API + Socket.IO server

The app supports:

- User signup and login
- Persistent authentication with JWT stored in HTTP-only cookies
- Profile image uploads
- One-to-one chat messaging
- Image attachments in messages
- Online user presence
- Theme switching in the UI

## Tech Stack

### Frontend

- React 19
- Vite 7
- React Router
- Zustand
- Axios
- Socket.IO client
- Tailwind CSS
- DaisyUI
- Lucide React
- React Hot Toast

### Backend

- Node.js
- Express 5
- MongoDB with Mongoose
- Socket.IO
- JWT (`jsonwebtoken`)
- `bcryptjs`
- `cookie-parser`
- `cors`
- Cloudinary
- `dotenv`

## Project Structure

```text
chat-app/
|-- backend/
|   |-- src/
|   |   |-- controllers/
|   |   |-- lib/
|   |   |-- middleware/
|   |   |-- models/
|   |   |-- routes/
|   |   `-- index.js
|   `-- package.json
|-- frontend/
|   |-- src/
|   |   |-- components/
|   |   |-- constants/
|   |   |-- lib/
|   |   |-- pages/
|   |   `-- store/
|   `-- package.json
|-- package.json
`-- README.md
```

## How It Works

### Authentication Flow

- Users sign up or log in through `/api/auth/*` routes.
- The backend creates a JWT and stores it in a `jwt` HTTP-only cookie.
- The frontend sends requests with `withCredentials: true`.
- Protected backend routes use `protectRoute` middleware to validate the cookie and attach the authenticated user to `req.user`.

### Real-Time Messaging

- The backend creates an HTTP server and attaches a Socket.IO server to it.
- When a user logs in, the frontend opens a socket connection and sends `userId` in the connection query.
- The backend stores a `userId -> socketId` mapping in memory.
- Connected clients receive `getOnlineUsers` events.
- When a message is sent, the backend emits `newMessage` directly to the receiver if they are online.

### Media Uploads

- Profile pictures are uploaded to Cloudinary in `updateProfile`.
- Message images are uploaded to Cloudinary before the message document is saved.

## Features

### Implemented UI Pages

- Home chat layout with sidebar and active conversation panel
- Signup page
- Login page
- Profile page
- Theme settings page

### Chat Features

- Contact sidebar
- Online/offline indicators
- Optional "show online only" filter
- Message list with timestamp rendering
- Text messages
- Image attachments

### Profile Features

- View account info
- Upload and update avatar
- Show member since date

## API Routes

Base API URL configured in the frontend:

`http://localhost:5001/api`

### Auth Routes

- `POST /api/auth/signup`
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `PUT /api/auth/update-profile` (protected)
- `GET /api/auth/check` (protected)

### Message Routes

- `GET /api/messages/user` (protected, sidebar users)
- `GET /api/messages/:id` (protected, conversation with a user)
- `POST /api/messages/send/:id` (protected, send message)

## Data Models

### User

- `email`
- `fullName`
- `password`
- `profilePic`
- timestamps

### Message

- `senderId`
- `receiverId`
- `text`
- `image`
- timestamps

## Environment Variables

Create a `.env` file inside `backend/` with the following values:

```env
PORT=5001
MONGODB_URI=your_mongodb_connection_string
JWT_SECRET=your_jwt_secret
CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_API_SECRET=your_cloudinary_api_secret
NODE_ENV=development
```

## Installation

This project uses `pnpm`.

### 1. Install root dependencies

The root package mainly provides convenience scripts:

```bash
pnpm install
```

### 2. Install backend dependencies

```bash
pnpm install --dir backend
```

### 3. Install frontend dependencies

```bash
pnpm install --dir frontend
```

## Running the Project

### Start the backend

```bash
pnpm --dir backend run dev
```

The backend listens on the `PORT` value from `backend/.env` and is expected to run on `http://localhost:5001`.

### Start the frontend

In a separate terminal:

```bash
pnpm --dir frontend run dev
```

By default, Vite runs on `http://localhost:5173`.

## Root Scripts

Available in the repository root:

- `pnpm run build`
  - Installs backend and frontend dependencies, then builds the frontend
- `pnpm run start`
  - Starts the backend in production mode

## Frontend Scripts

Available in `frontend/package.json`:

- `pnpm --dir frontend run dev`
- `pnpm --dir frontend run build`
- `pnpm --dir frontend run lint`
- `pnpm --dir frontend run preview`

## Backend Scripts

Available in `backend/package.json`:

- `pnpm --dir backend run dev`
- `pnpm --dir backend run start`

## Deployment Notes

- The current root `build` script only builds the frontend.
- The backend serves API and Socket.IO traffic, but it does not currently serve the frontend build as static files.
- For production deployment, you will likely want:
  - an environment-specific frontend API URL
  - environment-specific CORS settings
  - a process manager or container setup

## Current Implementation Notes

The README above describes the intended structure and current feature set, but there are a few code-level inconsistencies worth knowing before you run it:

- Backend CORS currently uses `http://locahost:5173` in `backend/src/index.js` (typo in `localhost`).
- The frontend calls `GET /messages/users`, while the backend currently exposes `GET /api/messages/user`.
- The socket connection helper in the auth store has a few method-call issues that may affect connect/disconnect behavior.
- There are a few controller typos and variable mismatches in the backend that may need cleanup before all flows work end to end.

## Suggested Next Improvements

- Add environment variable support in the frontend instead of hardcoded localhost URLs
- Add message validation and better error handling
- Add tests for auth and messaging routes
- Persist online presence in a more durable way if you scale beyond a single server instance
- Serve the frontend build from the backend for simpler production deployment

## License

This project currently uses the `ISC` license as defined in the package manifests.
