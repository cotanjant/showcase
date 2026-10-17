# Showcase

A Go web application with user management, cookie session authentication, and profile image uploads to Garage S3 storage.

## Architecture

The backend is built in Go using `net/http` and Ent ORM with a PostgreSQL database. Infrastructure and deployment manifests sit in `ops/` managed via OpenTofu and ArgoCD.

- **Backend**: Go 1.27 web server (`backend/main.go`)
- **Database**: PostgreSQL with Ent ORM (`backend/ent`)
- **Object Storage**: Garage S3 for uploaded profile images (`backend/garage`)
- **Deployment**: Docker multi-stage build, Kubernetes manifests via OpenTofu and ArgoCD (`ops/tofu`)

## API Endpoints

### Authentication
- `POST /auth/login` - Authenticate with username and password; sets an HTTP-only session cookie
- `POST /auth/logout` - Clear session cookie and remove session from database

### Users
- `POST /users` - Register a new user account
- `GET /users` - List all registered users
- `GET /users/{id}` - Get user details by ID
- `PATCH /users/{id}` - Update user profile (requires active session)
- `DELETE /users/{id}` - Delete user account (requires active session)
- `POST /users/profileupload` - Upload a profile picture to S3 storage (requires active session)

### Projects
- `GET /projects` - List projects

## Environment Variables

Copy `.env.example` to `.env` or pass the required environment variables:

```
DATABASE_URL=postgres://user:password@localhost:5432/dbname?sslmode=disable
AWS_ACCESS_KEY_ID=your_garage_key
AWS_SECRET_ACCESS_KEY=your_garage_secret
S3_ENDPOINT=http://localhost:3900
S3_BUCKET=showcase-profile-images
```

## Local Development

Run tests or build the service locally:

```bash
cd backend
go run main.go
```

To run database migrations:

```bash
cd backend
go run cmd/migrate/main.go
```

To build the Docker container:

```bash
docker build -t showcase .
```
