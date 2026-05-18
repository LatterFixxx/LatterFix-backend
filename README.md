# NestJS Backend with Authentication

A standard NestJS backend project with JWT authentication.

## Features

- JWT-based authentication
- User registration and login
- Password hashing with bcrypt
- Protected routes with guards
- Input validation
- Environment configuration

## Setup

1. Install dependencies:
```bash
npm install
```

2. Create `.env` file:
```bash
cp .env.example .env
```

3. Update `.env` with your configuration:
```
PORT=3000
JWT_SECRET=your-secret-key-change-in-production
JWT_EXPIRATION=1d
```

## Running the app

```bash
# Development
npm run start:dev

# Production
npm run build
npm run start:prod
```

## API Endpoints

### Authentication

- `POST /auth/register` - Register a new user
  ```json
  {
    "email": "user@example.com",
    "password": "password123",
    "name": "John Doe"
  }
  ```

- `POST /auth/login` - Login
  ```json
  {
    "email": "user@example.com",
    "password": "password123"
  }
  ```

### Protected Routes

Use the JWT token in the Authorization header:
```
Authorization: Bearer <your-token>
```

## Project Structure

```
src/
├── auth/              # Authentication module
│   ├── dto/          # Data transfer objects
│   ├── guards/       # Auth guards
│   └── strategies/   # Passport strategies
├── users/            # Users module
└── main.ts           # Application entry point
```
