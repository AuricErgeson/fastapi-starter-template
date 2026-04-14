# FastAPI Starter Template with Frontend Integration

## Overview
A full-stack starter template featuring a modern FastAPI backend with async database support and a responsive frontend presentation system for showcasing your projects.

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
# Run database migrations
alembic upgrade head
```

### 4. Start Development Server
```bash
# Backend API
uvicorn app.main:app --reload

# Frontend application will be served from /frontend directory
```

## 📁 Project Structure

```
├── app/                    # FastAPI Backend
│   ├── api/v1/            # API endpoints and routing
│   │   └── routes/        # Route modules
│   ├── core/              # Core configuration (settings.py:4)
│   ├── db/                # Database configuration (session.py:5)
│   ├── models/            # SQLAlchemy models
│   └── schemas/           # Pydantic schemas
├── frontend/              # Frontend Presentation System
│   ├── presentations/     # HTML presentations
│   ├── styles/           # CSS stylesheets
│   └── scripts/          # JavaScript utilities
├── alembic/              # Database migrations
├── tests/                # Test directory
└── docs/                 # Documentation
```

## 🛠️ Backend Architecture

### Core Configuration (`app/core/settings.py:4`)
- Environment-based settings using Pydantic Settings
- Database URL configuration
- Debug mode and app name settings

### Database Layer (`app/db/session.py:5`)
- Async SQLAlchemy engine with asyncpg driver
- Async session management with dependency injection
- Declarative base model setup

### API Structure (`app/api/v1/router.py:5`)
- Versioned API routing with `/api/v1` prefix
- Modular route inclusion system
- Health check endpoint available at `/api/v1/health`

## 🎨 Frontend Presentation System

### Creating Presentations
```bash
# Create a new presentation
python scripts/create_presentation.py --name "Project Demo" --output frontend/presentations/

# Convert PowerPoint to web format
python scripts/convert_ppt.py --input presentation.pptx --output frontend/presentations/
```

### Presentation Features
- **Zero Dependencies**: Single HTML files with inline CSS/JS
- **Responsive Design**: Fit-to-viewport with 100vh/100dvh constraints
- **Animation Rich**: CSS animations with prefers-reduced-motion support
- **In-Browser Editing**: Edit text directly in browser (optional)

### Presentation Types
1. **Project Showcase**: Pitch decks for demos and presentations
2. **API Documentation**: Interactive API documentation slides
3. **Development Workflow**: Team process and architecture overviews
4. **Feature Walkthroughs**: Step-by-step feature demonstrations

### Viewport Fitting Rules (NON-NEGOTIABLE)
Every slide follows these invariants:
- `.slide` must have `height: 100vh; height: 100dvh; overflow: hidden;`
- Font sizes use `clamp(min, preferred, max)` - never fixed px/rem
- Images: `max-height: min(50vh, 400px)`
- Breakpoints for heights: 700px, 600px, 500px

### Content Density Limits
| Slide Type | Maximum Content |
|------------|-----------------|
| Title slide | 1 heading + 1 subtitle |
| Content slide | 1 heading + 4-6 bullet points |
| Feature grid | 1 heading + 6 cards maximum |
| Code slide | 1 heading + 8-10 lines of code |
| Image slide | 1 heading + 1 image (max 60vh height) |

**Content exceeds limits? Split into multiple slides. Never cram, never scroll.**

## 🔧 Development Workflow

### Database Migrations
```bash
# Create new migration
alembic revision --autogenerate -m "Description"

# Apply migrations
alembic upgrade head

# Rollback migration
alembic downgrade -1
```

### Testing
```bash
# Run tests
pytest

# Run tests with coverage
pytest --cov=app tests/
```

### Code Quality
```bash
# Format code
black app/

# Sort imports
isort app/

# Type checking
mypy app/
```

## 💡 Frontend Design Principles

### 1. Zero-Dependency Architecture
- Single self-contained HTML files
- Inline CSS and JavaScript
- No npm, no build tools required

### 2. Show, Don't Tell
- Generate visual previews before committing to design
- Users discover preferences by seeing options
- Avoid abstract design discussions

### 3. Distinctive Aesthetics
- **Typography**: Choose beautiful, unique fonts (avoid generic Arial/Inter)
- **Color & Theme**: Cohesive palettes with sharp accents
- **Motion**: CSS animations with proper timing and delays
- **Backgrounds**: Layered gradients and geometric patterns

### 4. Avoid AI Slop
- ❌ Overused font families (Inter, Roboto, system fonts)
- ❌ Cliched color schemes (purple gradients on white)
- ❌ Predictable layouts
- ✅ Creative, context-specific designs
- ✅ Unexpected choices that feel genuinely designed

## 📊 API Endpoints

### Health Check
```
GET /api/v1/health
Response: {"status": "ok"}
```

### Frontend Presentations
```
GET /presentations/{name}.html
Response: HTML presentation with embedded CSS/JS
```

## 🚀 Deployment

### Backend Deployment
```bash
# Production server with Gunicorn
gunicorn app.main:app -w 4 -k uvicorn.workers.UvicornWorker

# Docker deployment
docker build -t fastapi-app .
docker run -p 8000:8000 fastapi-app
```

### Frontend Deployment
- Presentations are static HTML files
- Deploy to any static hosting (Netlify, Vercel, GitHub Pages)
- CDN for global distribution

## 🔄 Database Support

The template supports:
- PostgreSQL (async via asyncpg) - Default
- SQLite (for development/testing)
- MySQL (with async support)

Configure in `.env`:
```env
# PostgreSQL (recommended)
DATABASE_URL=postgresql+asyncpg://user:password@localhost:5432/dbname

# SQLite
DATABASE_URL=sqlite+aiosqlite:///./app.db
```

## 🧪 Testing Setup

### Backend Tests
```python
# tests/test_api.py
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

### Frontend Tests
- Browser testing with Playwright
- Visual regression testing
- Accessibility audits

## 🎯 Best Practices

### Backend
- Use async/await for database operations
- Implement proper error handling
- Add API versioning from the start
- Document endpoints with OpenAPI/Swagger

### Frontend
- Always test viewport fitting at 1280x720
- Include `prefers-reduced-motion` support
- Use semantic HTML elements
- Optimize images before embedding

### Security
- Environment variables for secrets
- Input validation with Pydantic
- CORS configuration for frontend access
- Rate limiting for API endpoints

## 📈 Monitoring & Observability

### Backend Monitoring
```python
# app/core/monitoring.py
import structlog
from prometheus_fastapi_instrumentator import Instrumentator

logger = structlog.get_logger()
instrumentator = Instrumentator()
```

### Frontend Analytics
- Custom event tracking for presentation interactions
- Performance monitoring with Web Vitals
- Error tracking with Sentry

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Ensure all tests pass
5. Submit a pull request

### Commit Guidelines
- `feat: ` New feature
- `fix: ` Bug fix
- `docs: ` Documentation
- `style: ` Code style (formatting, etc.)
- `refactor: ` Code refactoring
- `test: ` Adding tests
- `chore: ` Maintenance tasks

## 📚 Additional Resources

### Backend Development
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [Alembic Documentation](https://alembic.sqlalchemy.org/)

### Frontend Presentation
- [CSS Tricks: Viewport Units](https://css-tricks.com/fun-viewport-units/)
- [MDN: CSS clamp()](https://developer.mozilla.org/en-US/docs/Web/CSS/clamp)
- [A Complete Guide to CSS Media Queries](https://css-tricks.com/a-complete-guide-to-css-media-queries/)

### Design Inspiration
- [Fontshare](https://www.fontshare.com/) - Beautiful free fonts
- [Coolors](https://coolors.co/) - Color palette generator
- [UI Movement](https://uimovement.com/) - UI animation inspiration

## 🆘 Troubleshooting

### Common Issues

**Database Connection Failed**
```bash
# Check PostgreSQL is running
sudo service postgresql status

# Test connection
psql -U postgres -h localhost
```

**Migration Errors**
```bash
# Reset migrations (development only)
alembic downgrade base
alembic upgrade head
```

**Frontend Not Loading**
- Check browser console for errors
- Verify file paths are correct
- Ensure CORS is configured

### Getting Help
1. Check the troubleshooting section above
2. Review the example `.env` configuration
3. Look at existing test cases
4. Search closed issues

## 📄 License

MIT License - see LICENSE file for details.

## 🙏 Acknowledgments

- FastAPI framework by Sebastián Ramírez
- Frontend Slides skill for presentation system
- All contributors and maintainers

---

**Ready to start?** Run `cp .env.example .env` and customize your settings!