# FastAPI Starter Template

## Overview
A modern, production-ready FastAPI starter template with async database support, structured architecture, and all essential tools pre-configured. Perfect for building scalable API backends.

## 🚀 Quick Start

### 1. Environment Setup
```bash
# Copy environment template
cp .env.example .env

# Edit .env with your database credentials
# Default: DATABASE_URL=postgresql+asyncpg://postgres:password@localhost:5432/mydb
```

### 2. Install Dependencies
```bash
# Using UV (recommended)
uv sync

# Or traditional pip
pip install -e .
```

### 3. Database Setup
```bash
# Run database migrations (create first migration if needed)
alembic upgrade head
```

### 4. Start Development Server
```bash
uvicorn app.main:app --reload
```

Server will be available at `http://localhost:8000`

## 📁 Actual Project Structure

```
fastapi-starter-template/
├── .env.example             # Environment template
├── pyproject.toml          # Python dependencies and configuration
├── alembic.ini             # Alembic configuration
├── README.md               # This file
├── LICENSE                 # MIT License
├── alembic/                # Database migrations
│   ├── env.py              # Async migration setup
│   └── versions/           # Migration files
└── app/                    # FastAPI Application
    ├── main.py             # Application entry point
    ├── api/v1/             # API endpoints (v1)
    │   ├── router.py       # Main API router (app/api/v1/router.py:5)
    │   └── routes/         # Route modules
    │       └── health.py   # Health check endpoint
    ├── core/               # Core configuration
    │   └── settings.py     # App settings (app/core/settings.py:4)
    ├── db/                 # Database configuration
    │   ├── base.py         # SQLAlchemy base class
    │   └── session.py      # Async session setup (app/db/session.py:5)
    ├── models/             # SQLAlchemy models (ready for your models)
    └── schemas/            # Pydantic schemas (ready for your schemas)
```

## 🛠️ Core Components

### Main Application (`app/main.py`)
```python
from fastapi import FastAPI
from app.api.v1.router import api_router
from app.core.settings import settings

app = FastAPI(title=settings.app_name, debug=settings.debug)
app.include_router(api_router)
```

### Configuration Management (`app/core/settings.py:4`)
- Environment-based settings using Pydantic Settings v2
- Loads from `.env` file automatically
- Configurable app name, debug mode, and database URL

### Database Layer (`app/db/session.py:5`)
- Async SQLAlchemy engine with asyncpg driver
- Async session management with dependency injection
- Declarative base model setup (`app/db/base.py`)

### API Structure (`app/api/v1/router.py:5`)
- Versioned API routing with `/api/v1` prefix
- Modular route inclusion system
- Currently includes health check endpoint at `/api/v1/health`

### Alembic Migrations (`alembic/env.py`)
- Async-compatible database migrations
- Integrated with application settings
- Auto-generate migrations from model changes

## 🌐 API Endpoints

### Health Check
```
GET /api/v1/health
Response: {"status": "ok"}
```

Test with:
```bash
curl http://localhost:8000/api/v1/health
```

## 📦 Dependencies

### Core Dependencies
- `fastapi>=0.111.0` - Modern web framework
- `uvicorn[standard]>=0.30.0` - ASGI server
- `sqlalchemy>=2.0.0` - Async ORM with SQLAlchemy 2.0
- `alembic>=1.13.0` - Database migrations
- `asyncpg>=0.29.0` - Async PostgreSQL driver
- `pydantic-settings>=2.0.0` - Settings management
- `python-dotenv>=1.0.0` - Environment variables

### Development Dependencies
- `httpx>=0.27.0` - Async HTTP client for testing
- `pytest>=8.0.0` - Testing framework
- `pytest-asyncio>=0.23.0` - Async test support

### Build System
- Uses `uv` - modern Python package manager
- Lock file: `uv.lock`

## 🔄 Database Support

### PostgreSQL (Recommended)
```env
DATABASE_URL=postgresql+asyncpg://user:password@localhost:5432/dbname
```

### SQLite (Development/Testing)
```env
DATABASE_URL=sqlite+aiosqlite:///./app.db
```

### Create Your First Model
```python
# app/models/user.py
from sqlalchemy import String, Integer
from sqlalchemy.orm import Mapped, mapped_column
from app.db.base import Base

class User(Base):
    __tablename__ = "users"
    
    id: Mapped[int] = mapped_column(Integer, primary_key=True, index=True)
    email: Mapped[str] = mapped_column(String, unique=True, index=True)
    hashed_password: Mapped[str] = mapped_column(String)
    is_active: Mapped[bool] = mapped_column(default=True)
```

### Generate Migration
```bash
# After adding your model
alembic revision --autogenerate -m "Add users table"
alembic upgrade head
```

## 🧪 Testing

### Run Tests
```bash
pytest
```

### Test Structure (To be created)
```
tests/
├── conftest.py          # Test fixtures
├── test_api/           # API endpoint tests
├── test_models/        # Model tests
└── test_db/           # Database tests
```

### Example Test
```python
# tests/test_api/test_health.py
import pytest
from httpx import AsyncClient
from app.main import app

@pytest.mark.asyncio
async def test_health_check():
    async with AsyncClient(app=app, base_url="http://test") as client:
        response = await client.get("/api/v1/health")
        assert response.status_code == 200
        assert response.json() == {"status": "ok"}
```

## 🚀 Development Workflow

### 1. Add a New API Route
```python
# app/api/v1/routes/users.py
from fastapi import APIRouter, Depends
from sqlalchemy.ext.asyncio import AsyncSession
from app.db.session import get_db

router = APIRouter()

@router.get("/users")
async def get_users(db: AsyncSession = Depends(get_db)):
    return {"users": []}
```

```python
# app/api/v1/router.py - Add to imports
from app.api.v1.routes.users import router as users_router

# Add to router
api_router.include_router(users_router, prefix="/users", tags=["users"])
```

### 2. Create Pydantic Schemas
```python
# app/schemas/user.py
from pydantic import BaseModel, EmailStr

class UserBase(BaseModel):
    email: EmailStr

class UserCreate(UserBase):
    password: str

class User(UserBase):
    id: int
    is_active: bool
    
    class Config:
        from_attributes = True
```

### 3. Run Development Server with Auto-Reload
```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

## 📊 Development Tools

### Code Quality
```bash
# Format code (install black first)
black app/

# Sort imports (install isort first)
isort app/

# Type checking (install mypy first)
mypy app/
```

### Database Operations
```bash
# Create new migration
alembic revision --autogenerate -m "Description"

# Apply migrations
alembic upgrade head

# Rollback migration
alembic downgrade -1

# Show migration history
alembic history --verbose
```

### Interactive API Documentation
- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`
- OpenAPI JSON: `http://localhost:8000/openapi.json`

## 🚢 Deployment

### Production Server
```bash
# Install production dependencies
pip install gunicorn

# Run with Gunicorn
gunicorn app.main:app -w 4 -k uvicorn.workers.UvicornWorker
```

### Docker Deployment
```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install uv
RUN pip install uv

# Copy dependency files
COPY pyproject.toml uv.lock ./

# Install dependencies
RUN uv sync --frozen

# Copy application code
COPY . .

# Run migrations and start server
CMD alembic upgrade head && uvicorn app.main:app --host 0.0.0.0 --port $PORT
```

### Environment Variables for Production
```env
APP_NAME="FastAPI Production"
DEBUG=False
DATABASE_URL=postgresql+asyncpg://user:password@production-db/dbname
```

## 🔧 Configuration

### Settings (`app/core/settings.py:4`)
The application uses Pydantic Settings which automatically:
1. Loads from `.env` file
2. Validates environment variables
3. Provides type hints and auto-completion
4. Supports nested settings models

### Customize Settings
```python
# app/core/settings.py
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    app_name: str = "FastAPI Starter"
    debug: bool = False
    database_url: str
    api_prefix: str = "/api/v1"
    
    # Add your custom settings
    secret_key: str
    allowed_hosts: list[str] = ["localhost", "127.0.0.1"]
    
    model_config = SettingsConfigDict(env_file=".env")
```

## 🎯 Best Practices Implemented

### Backend Architecture
- ✅ Async/await for all database operations
- ✅ Dependency injection for database sessions
- ✅ Versioned API from the start
- ✅ Environment-based configuration
- ✅ Modular route structure

### Database
- ✅ Async SQLAlchemy 2.0
- ✅ Alembic migrations
- ✅ PostgreSQL with asyncpg (production-ready)
- ✅ SQLite for development/testing

### Development Experience
- ✅ Auto-reload during development
- ✅ Interactive API documentation
- ✅ Structured project layout
- ✅ Modern dependency management with `uv`

### Security
- ✅ Environment variables for secrets
- ✅ Input validation via Pydantic
- ✅ Async database operations
- ✅ CORS configurable via settings

## 🆘 Troubleshooting

### Common Issues

**Database Connection Failed**
```bash
# Check PostgreSQL is running
pg_isready -h localhost -p 5432

# Test connection
psql -U postgres -h localhost
```

**Migration Errors**
```bash
# Reset migrations (development only)
alembic downgrade base
alembic upgrade head
```

**Dependency Issues**
```bash
# Clean install with uv
uv sync --clean
```

**Port Already in Use**
```bash
# Find and kill process on port 8000
lsof -ti:8000 | xargs kill -9
```

### Getting Help
1. Check the interactive docs at `http://localhost:8000/docs`
2. Review the `.env.example` file for required variables
3. Check Alembic logs in `alembic.ini`
4. Enable debug mode in `.env`: `DEBUG=True`

## 📈 Next Steps

### 1. Add Authentication
- JWT token-based auth
- OAuth2 with password flow
- User registration/login endpoints

### 2. Implement CRUD Operations
- Complete user management
- Resource creation/updating/deletion
- Pagination and filtering

### 3. Add Monitoring
- Health checks and metrics
- Logging configuration
- Error tracking

### 4. Create Test Suite
- Unit tests for models
- Integration tests for API
- Database test fixtures

## 📄 License

MIT License - see LICENSE file for details.

## 🙏 Acknowledgments

- [FastAPI](https://fastapi.tiangolo.com/) by Sebastián Ramírez
- [SQLAlchemy](https://www.sqlalchemy.org/) for the ORM
- [Alembic](https://alembic.sqlalchemy.org/) for migrations
- [Pydantic](https://docs.pydantic.dev/) for data validation

---

**Happy Coding!** 🚀

Start by creating your first model and migration:
```bash
# 1. Edit app/models/__init__.py to import your models
# 2. Create alembic migration
alembic revision --autogenerate -m "Initial models"
# 3. Apply migration
alembic upgrade head
# 4. Run the server
uvicorn app.main:app --reload
```