
# FastAPI Blog

A full-stack blog application built with **FastAPI**, **PostgreSQL**, **SQLAlchemy 2.0**, and **Jinja2**. The project includes JWT-based authentication, user account management, post CRUD operations, profile-picture handling, password reset via email, pagination, database migrations, and automated API tests.

## Features

### Authentication & Users
- User registration with Pydantic validation
- Secure password hashing with `pwdlib`
- JWT access-token authentication
- OAuth2 password flow
- Protected user endpoints
- Get current authenticated user
- Update username and email
- Delete user account
- Change password
- Forgot-password flow with expiring reset tokens
- Password reset through email
- Profile-picture upload and deletion
- Image validation, resizing, EXIF correction, and compression
- Maximum profile-image upload size of 5 MB

### Blog Posts
- Create posts
- Retrieve a single post
- Update posts completely with `PUT`
- Partially update posts with `PATCH`
- Delete posts
- Authorization so users can only modify their own posts
- Paginated post listings
- Paginated posts for individual users
- Post author information included in API responses
- Post likes field with an Alembic migration

### Web Interface
The application also includes a server-rendered web interface using **Jinja2 templates**, with pages for:
- Home / post listing
- Individual posts
- User posts
- Login
- Registration
- Account management
- Forgot password
- Reset password
- Error handling

### Reliability & Testing
- Async database access using SQLAlchemy
- Health-check endpoint
- Custom API and HTML exception handlers
- Automated async API tests with `pytest` and `httpx`
- Database fixtures with transactional rollback
- Mock AWS environment using `moto` in tests

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
│       ├── 1c58dc494b99_initial_schema.py
│       └── 9649c460126e_add_likes_to_posts.py
├── media/
│   └── profile_pics/
├── routers/
│   ├── posts.py
│   └── users.py
├── static/
├── templates/
├── tests/
│   ├── conftest.py
│   ├── test_posts.py
│   └── test_users.py
├── auth.py
├── config.py
├── database.py
├── email_utils.py
├── image_utils.py
├── main.py
├── models.py
├── schemas.py
├── populate_db.py
├── alembic.ini
└── .gitignore
```

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

The repository currently does not include a `requirements.txt` file, so install the dependencies used by the project:

```bash
pip install fastapi uvicorn sqlalchemy "psycopg[binary]" alembic \
pydantic pydantic-settings email-validator python-multipart \
jinja2 pillow pyjwt "pwdlib[argon2]" aiosmtplib boto3 \
httpx pytest anyio "moto[s3]"
```

For a cleaner setup, freeze the working environment into a dependency file:

```bash
pip freeze > requirements.txt
```

Then future installations can use:

```bash
pip install -r requirements.txt
```

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/fastapi-blog.git
cd fastapi-blog
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

If you have not created `requirements.txt` yet, install the dependencies from the Requirements section above.

### 4. Create the PostgreSQL database

Create a PostgreSQL database and user, then configure the connection string in your environment variables.

Example:

```text
postgresql+psycopg://username:password@localhost:5432/fastapi_blog
```

### 5. Configure environment variables

Create a `.env` file in the project root.

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

**Never commit `.env` to GitHub.** The repository's `.gitignore` is configured to exclude environment files.

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

The test suite uses `moto` to mock AWS services and uses a separate PostgreSQL test database configured in `tests/conftest.py`.

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

## License

This project is intended as a learning and portfolio project.
