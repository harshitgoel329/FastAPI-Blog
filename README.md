# FastAPI Blog

A production-oriented full-stack blogging platform built with **FastAPI**, **PostgreSQL**, **SQLAlchemy**, **JWT authentication**, and **Jinja2**. The application provides secure user authentication, blog post CRUD operations, profile management, password reset, image processing, pagination, database migrations, and automated testing.

## Demo

Add screenshots or a short demo video/GIF here to showcase the application interface and key workflows.

## Features

### Authentication & Authorization
- User registration and login
- JWT-based authentication
- OAuth2 password flow
- Protected API endpoints
- Ownership-based authorization
- Secure password hashing

### Blog Posts
- Create, read, update, and delete posts
- Full and partial post updates
- Paginated post listings
- User-specific posts
- Post ownership validation

### User Management
- Update account information
- Change password
- Profile-picture upload and deletion
- Image validation and processing

### Password Recovery
- Forgot-password flow
- Secure reset tokens
- Token expiration
- Password reset through email

### Database & Backend
- PostgreSQL database
- SQLAlchemy ORM
- Alembic migrations
- Async database operations
- Pydantic request/response validation

### Testing
- Automated API tests
- Async testing with HTTPX
- Pytest fixtures
- Database isolation for tests
- AWS S3 mocking with Moto

## Tech Stack

| Category | Technology |
|---|---|
| Backend | FastAPI |
| Language | Python |
| Database | PostgreSQL |
| ORM | SQLAlchemy 2.0 |
| Database Driver | psycopg |
| Migrations | Alembic |
| Validation | Pydantic |
| Authentication | JWT + OAuth2 |
| Password Hashing | pwdlib |
| Templates | Jinja2 |
| Image Processing | Pillow |
| Email | aiosmtplib |
| Testing | pytest + HTTPX |
| AWS Mocking | moto |
| Async Runtime | asyncio |

## Project Structure

```text
FastAPI-Blog/
├── alembic/
│   └── versions/
├── media/
│   └── profile_pics/
├── routers/
├── static/
├── templates/
├── tests/
├── .env.example
├── .gitignore
├── README.md
├── requirements.txt
├── alembic.ini
├── auth.py
├── config.py
├── database.py
├── email_utils.py
├── image_utils.py
├── main.py
├── models.py
├── populate_db.py
└── schemas.py
```

> `.env` and `.venv/` are intentionally excluded from the repository through `.gitignore`.

## How It Works

The application follows a layered backend architecture:

1. The client sends HTTP requests to FastAPI routes.
2. Router modules handle endpoint-specific logic.
3. Pydantic schemas validate incoming and outgoing data.
4. Authentication dependencies validate JWT access tokens for protected endpoints.
5. SQLAlchemy handles database operations against PostgreSQL.
6. Alembic manages database schema migrations.
7. Utility modules handle email delivery and profile-image processing.
8. Pytest and HTTPX are used to test the API independently of the production application.

## API Endpoints

### Users

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| `POST` | `/api/users` | Register a user | No |
| `POST` | `/api/users/token` | Login and receive JWT | No |
| `GET` | `/api/users/me` | Get current user | Yes |
| `GET` | `/api/users/{user_id}` | Get public user information | No |
| `GET` | `/api/users/{user_id}/posts` | Get user's posts | No |
| `PATCH` | `/api/users/{user_id}` | Update user information | Yes |
| `DELETE` | `/api/users/{user_id}` | Delete user | Yes |
| `PATCH` | `/api/users/{user_id}/picture` | Upload profile picture | Yes |
| `DELETE` | `/api/users/{user_id}/picture` | Delete profile picture | Yes |
| `PATCH` | `/api/users/me/password` | Change password | Yes |
| `POST` | `/api/users/forgot-password` | Request password reset | No |
| `POST` | `/api/users/reset-password` | Reset password | No |

### Posts

| Method | Endpoint | Description | Auth |
|---|---|---|---|
| `GET` | `/api/posts` | Get paginated posts | No |
| `POST` | `/api/posts` | Create a post | Yes |
| `GET` | `/api/posts/{post_id}` | Get a post | No |
| `PUT` | `/api/posts/{post_id}` | Replace a post | Yes |
| `PATCH` | `/api/posts/{post_id}` | Partially update a post | Yes |
| `DELETE` | `/api/posts/{post_id}` | Delete a post | Yes |

### Health Check

```http
GET /health
```

Returns:

```json
{
  "status": "healthy"
}
```

## API Documentation

Once the application is running, FastAPI automatically provides interactive API documentation:

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`

## Requirements

The project requires:

- Python 3.10+
- PostgreSQL
- A configured email/SMTP server if password-reset emails are used

All Python dependencies are listed in `requirements.txt`.

Install them with:

```bash
pip install -r requirements.txt
```

## Environment Variables

Create a `.env` file in the project root based on `.env.example`.

Example:

```env
DATABASE_URL=postgresql+psycopg://username:password@localhost:5432/fastapi_blog
SECRET_KEY=replace-with-a-long-random-secret
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

MAX_UPLOAD_SIZE_BYTES=5242880
POSTS_PER_PAGE=10
RESET_TOKEN_EXPIRE_MINUTES=60

MAIL_SERVER=smtp.example.com
MAIL_PORT=587
MAIL_USERNAME=your-email@example.com
MAIL_PASSWORD=your-email-password
MAIL_FROM=your-email@example.com
MAIL_USE_TLS=true

FRONTEND_URL=http://localhost:8000
```

Never commit `.env` to GitHub. It may contain database credentials, JWT secrets, and email credentials.

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/harshitgoel329/FastAPI-Blog.git
cd FastAPI-Blog
```

### 2. Create a virtual environment

Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

macOS / Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Create the PostgreSQL database

Create a PostgreSQL database and user, then configure the connection string in your `.env` file.

Example:

```text
postgresql+psycopg://username:password@localhost:5432/fastapi_blog
```

### 5. Configure environment variables

Create `.env` from `.env.example` and fill in your local PostgreSQL, JWT, and email configuration.

### 6. Run database migrations

```bash
alembic upgrade head
```

## Running the Application

Start the development server with:

```bash
uvicorn main:app --reload
```

Open:

```text
http://127.0.0.1:8000
```

The application serves both the HTML interface and the `/api` endpoints.

## Database Migrations

The project uses Alembic for schema migrations.

Apply the latest migrations:

```bash
alembic upgrade head
```

Create a new migration after changing SQLAlchemy models:

```bash
alembic revision --autogenerate -m "describe your change"
```

Then apply it:

```bash
alembic upgrade head
```

The project also currently creates missing tables during application startup through the FastAPI lifespan handler.

## Authentication

Authentication uses OAuth2 password flow with JWT access tokens.

To obtain a token:

```http
POST /api/users/token
```

The OAuth2 form uses:

```text
username = user's email
password = user's password
```

The response contains:

```json
{
  "access_token": "your-jwt-token",
  "token_type": "bearer"
}
```

Protected endpoints expect:

```http
Authorization: Bearer <access_token>
```

JWTs contain the authenticated user's ID as the `sub` claim and require both `sub` and `exp` claims during verification.

## Profile Pictures

Profile images are processed before being stored:

1. Validate the uploaded image.
2. Correct EXIF orientation.
3. Resize/crop to `300 × 300`.
4. Convert compatible image modes to RGB.
5. Save as optimized JPEG.
6. Generate a UUID-based filename.
7. Remove the previous profile picture when replaced.

The default maximum upload size is **5 MB**.

Uploaded profile pictures are stored locally under:

```text
media/profile_pics/
```

## Password Reset

The password-reset flow:

1. User submits an email to `/api/users/forgot-password`.
2. A secure random reset token is generated.
3. Only the SHA-256 hash of the token is stored in the database.
4. The token receives an expiration time.
5. A reset email is sent in the background.
6. The user submits the token with a new password.
7. The token is deleted after successful password reset.

This prevents plaintext reset tokens from being stored in the database.

## Testing

The project contains asynchronous API tests using `pytest` and `httpx`.

Run the test suite with:

```bash
pytest
```

The tests cover functionality including:

- User registration
- Input validation
- Duplicate-email protection
- Login
- Authentication
- Post creation
- Post updates
- Authorization checks
- Profile-picture uploads
- Password-reset email triggering

The test suite uses `moto` to mock AWS services and a separate PostgreSQL test database configured in `tests/conftest.py`.

## Security Considerations

The project implements several security-oriented practices:

- Passwords are never stored directly; password hashes are stored instead.
- JWT access tokens have expiration times.
- JWT verification requires both `sub` and `exp`.
- Password-reset tokens are hashed before storage.
- Password-reset tokens expire.
- Password-reset requests return a generic response to avoid revealing whether an email is registered.
- Protected resources verify the authenticated user's ownership before modification.
- Sensitive environment variables are loaded from `.env`.
- `.env` and virtual environments are excluded through `.gitignore`.

## Future Improvements

Potential extensions include:

- Refresh-token authentication
- Role-based authorization
- Search and filtering for posts
- Comments
- Persistent post likes with a user-to-post relationship
- Cloud object storage for profile images
- Rate limiting
- Structured application logging
- Docker and Docker Compose setup
- CI/CD pipeline with automated tests
- Production deployment configuration

## Project Status

This project is a portfolio/learning project demonstrating a production-oriented FastAPI backend with authentication, database persistence, file processing, email workflows, migrations, and automated testing.

## License

This project is intended as a learning and portfolio project.
