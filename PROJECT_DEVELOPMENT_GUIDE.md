# Virtual Day Program - Project Development Guide

## Table of Contents

1. [Project Setup](#project-setup)
2. [Development Environment](#development-environment)
3. [Backend Development](#backend-development)
4. [Frontend Development](#frontend-development)
5. [Database Setup](#database-setup)
6. [Deployment](#deployment)
7. [Testing](#testing)
8. [Maintenance](#maintenance)

## Project Setup

### 1. Prerequisites

```bash
# Install Node.js (v18 or higher)
node --version
npm --version

# Install Git
git --version

# Install PostgreSQL (v14 or higher)
psql --version

# Install Docker (optional)
docker --version
```

### 2. Create Project Structure

```bash
mkdir virtual-day-program
cd virtual-day-program

# Create project folder structure
mkdir -p {
  backend/{src/{controllers,models,routes,middleware,utils,config},tests},
  frontend/{src/{components,pages,hooks,utils,styles},public},
  database/{migrations,seeds},
  docs,
  scripts
}
```

### 3. Git Repository Setup

```bash
git init
echo "node_modules/
.env
.env.local
dist/
build/
*.log" > .gitignore

git add .
git commit -m "Initial project setup"
```

## Development Environment

### 1. Backend Environment Setup

#### Package Initialization

```bash
cd backend
npm init -y

# Install required dependencies
npm install express cors helmet morgan dotenv
npm install jsonwebtoken bcryptjs
npm install prisma @prisma/client
npm install socket.io
npm install nodemailer
npm install multer aws-sdk
npm install stripe
npm install joi express-rate-limit

# Dev dependencies
npm install -D typescript @types/node @types/express
npm install -D nodemon ts-node
npm install -D jest @types/jest supertest
npm install -D eslint prettier
```

#### TypeScript Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "lib": ["ES2020"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

#### Package.json scripts

```json
{
  "scripts": {
    "dev": "nodemon src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "test": "jest",
    "test:watch": "jest --watch",
    "lint": "eslint src/**/*.ts",
    "format": "prettier --write src/**/*.ts"
  }
}
```

### 2. Frontend Environment Setup

#### Create Next.js Project

```bash
cd ../frontend
npx create-next-app@latest . --typescript --tailwind --eslint --app

# Additional dependencies
npm install @radix-ui/react-dialog @radix-ui/react-dropdown-menu
npm install @radix-ui/react-tabs @radix-ui/react-toast
npm install lucide-react
npm install zustand
npm install react-hook-form @hookform/resolvers zod
npm install socket.io-client
npm install chart.js react-chartjs-2
npm install date-fns
npm install axios
```

## Backend Development

### 1. Project Structure

```
backend/
├── src/
│   ├── controllers/     # Route handlers
│   ├── models/         # Data models
│   ├── routes/         # API routes
│   ├── middleware/     # Custom middleware
│   ├── utils/          # Utility functions
│   ├── config/         # Configuration files
│   ├── services/       # Business logic
│   └── index.ts        # Entry point
├── tests/              # Test files
└── prisma/             # Database schema
```

### 2. Create Main Backend Files

#### src/index.ts

```typescript
import express from "express";
import cors from "cors";
import helmet from "helmet";
import morgan from "morgan";
import dotenv from "dotenv";
import { createServer } from "http";
import { Server } from "socket.io";

dotenv.config();

const app = express();
const server = createServer(app);
const io = new Server(server, {
  cors: {
    origin: process.env.FRONTEND_URL || "http://localhost:3000",
    methods: ["GET", "POST"],
  },
});

// Middleware
app.use(helmet());
app.use(cors());
app.use(morgan("combined"));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use("/api/auth", authRoutes);
app.use("/api/programs", programRoutes);
app.use("/api/sessions", sessionRoutes);
app.use("/api/users", userRoutes);

const PORT = process.env.PORT || 5000;
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### 3. Database Setup (Prisma)

#### prisma/schema.prisma

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        String   @id @default(uuid())
  email     String   @unique
  firstName String   @map("first_name")
  lastName  String   @map("last_name")
  role      UserRole @default(PARTICIPANT)
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")

  @@map("users")
}

enum UserRole {
  ADMIN
  INSTRUCTOR
  PARTICIPANT
  MODERATOR
}
```

### 4. Create API Routes

#### Authentication Routes

```typescript
// src/routes/auth.ts
import { Router } from "express";
import { AuthController } from "../controllers/auth.controller";
import { validateAuth } from "../middleware/validation";

const router = Router();
const authController = new AuthController();

router.post("/register", validateAuth.register, authController.register);
router.post("/login", validateAuth.login, authController.login);
router.post("/refresh", authController.refreshToken);
router.post("/logout", authController.logout);

export default router;
```

## Frontend Development

### 1. Project Structure

```
frontend/
├── src/
│   ├── app/            # Next.js App Router
│   ├── components/     # Reusable components
│   ├── hooks/          # Custom hooks
│   ├── lib/            # Utilities and configurations
│   ├── store/          # State management
│   ├── types/          # TypeScript types
│   └── styles/         # Global styles
└── public/             # Static assets
```

### 2. Main Frontend Components

#### Dashboard Layout

```typescript
// src/components/layout/dashboard-layout.tsx
import { Sidebar } from "./sidebar";
import { Header } from "./header";

interface DashboardLayoutProps {
  children: React.ReactNode;
}

export function DashboardLayout({ children }: DashboardLayoutProps) {
  return (
    <div className="flex h-screen bg-gray-100">
      <Sidebar />
      <div className="flex-1 flex flex-col overflow-hidden">
        <Header />
        <main className="flex-1 overflow-x-hidden overflow-y-auto bg-gray-100">
          <div className="container mx-auto px-6 py-8">{children}</div>
        </main>
      </div>
    </div>
  );
}
```

### 3. State Management (Zustand)

```typescript
// src/store/auth-store.ts
import { create } from "zustand";
import { User } from "../types/user";

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (user: User, token: string) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  token: null,
  isAuthenticated: false,
  login: (user, token) => set({ user, token, isAuthenticated: true }),
  logout: () => set({ user: null, token: null, isAuthenticated: false }),
}));
```

## Database Setup

### 1. PostgreSQL Setup

```bash
# Install PostgreSQL (macOS)
brew install postgresql
brew services start postgresql

# Create database
createdb virtual_day_program

# Run SQL script
psql -d virtual_day_program -f database.sql
```

### 2. Environment Variables

```env
# .env
DATABASE_URL="postgresql://username:password@localhost:5432/virtual_day_program"
JWT_SECRET="your-jwt-secret-key"
JWT_REFRESH_SECRET="your-refresh-secret-key"
FRONTEND_URL="http://localhost:3000"
AWS_ACCESS_KEY_ID="your-aws-key"
AWS_SECRET_ACCESS_KEY="your-aws-secret"
STRIPE_SECRET_KEY="your-stripe-key"
EMAIL_HOST="smtp.gmail.com"
EMAIL_USER="your-email@gmail.com"
EMAIL_PASS="your-app-password"
```

## Deployment

### 1. Production Build

```bash
# Backend build
cd backend
npm run build

# Frontend build
cd ../frontend
npm run build
```

### 2. Docker Setup

```dockerfile
# Dockerfile (backend)
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY dist ./dist
EXPOSE 5000
CMD ["node", "dist/index.js"]
```

### 3. Vercel Deployment (Frontend)

```bash
npm install -g vercel
vercel --prod
```

### 4. AWS Deployment (Backend)

```bash
# AWS CLI setup
aws configure

# Docker image build and push
docker build -t virtual-day-program-backend .
docker tag virtual-day-program-backend:latest your-account.dkr.ecr.region.amazonaws.com/virtual-day-program:latest
docker push your-account.dkr.ecr.region.amazonaws.com/virtual-day-program:latest
```

## Testing

### 1. Unit Tests

```typescript
// tests/auth.test.ts
import request from "supertest";
import app from "../src/app";

describe("Authentication", () => {
  test("should register a new user", async () => {
    const response = await request(app).post("/api/auth/register").send({
      email: "test@example.com",
      password: "password123",
      firstName: "Test",
      lastName: "User",
    });

    expect(response.status).toBe(201);
    expect(response.body.user.email).toBe("test@example.com");
  });
});
```

### 2. Integration Tests

```bash
npm run test
npm run test:watch
```

## Maintenance

### 1. Logging and Monitoring

```typescript
// src/utils/logger.ts
import winston from "winston";

export const logger = winston.createLogger({
  level: "info",
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: "error.log", level: "error" }),
    new winston.transports.File({ filename: "combined.log" }),
  ],
});
```

### 2. Database Backup

```bash
# Daily backup script
#!/bin/bash
pg_dump virtual_day_program > backup_$(date +%Y%m%d).sql
```

### 3. Performance Optimization

- Database indexing
- Query optimization
- Caching strategies
- Image optimization
- Code splitting

## Development Workflow

1. Project setup and environment configuration
2. Database schema implementation
3. Basic authentication system
4. User management APIs

### Phase 2: Core Features (Week 3-6)

1. Program management system
2. Session management
3. Enrollment and payment integration
4. Basic frontend dashboard

### Phase 3: Advanced Features (Week 7-10)

1. Live session integration (WebRTC)
2. Real-time messaging
3. Analytics and reporting
4. Advanced UI components

### Phase 4: Testing & Deployment (Week 11-12)

1. Comprehensive testing
2. Performance optimization
3. Production deployment
4. Documentation and training

## Helper Commands

```bash
# Development commands
npm run dev          # Start development server
npm run build        # Build for production
npm run test         # Run tests
npm run lint         # Check code style
npm run format       # Format code

# Database commands
npx prisma migrate dev     # Run migrations
npx prisma generate        # Generate Prisma client
npx prisma studio         # Open database GUI

# Git workflow
git checkout -b feature/new-feature
git add .
git commit -m "Add new feature"
git push origin feature/new-feature
```

Following this guide, you can create a complete Virtual Day Program system. Don't forget to perform detailed testing and code review at each stage.
