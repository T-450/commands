# API Scaffold Tool - GitHub Copilot Instructions

## Overview
Generate production-ready, scalable REST APIs with modern frameworks including complete architecture, security, testing, and deployment configuration.

## When to Use
Ask GitHub Copilot to use this pattern when:
- "Create a REST API for [feature]"
- "Generate API scaffolding for [domain]"
- "Build a backend API with [framework]"
- "Scaffold API endpoints for [resource]"

## Quick Start

### Basic API Generation
```
Generate a REST API for user management using FastAPI with:
- CRUD operations
- JWT authentication
- Input validation
- PostgreSQL database
- Comprehensive error handling
```

### With Specific Requirements
```
Create an Express.js API for e-commerce with:
- Product catalog endpoints
- Shopping cart functionality
- Order management
- Payment integration
- Rate limiting
- API documentation
```

## Framework Selection

### Choosing the Right Framework

**Prompt Copilot:**
```
Recommend the best API framework for this project:
- Primary language: [Python/JavaScript/Java/Go]
- Performance requirements: [high/medium/low]
- Team expertise: [experienced/intermediate/beginner]
- Use case: [microservices/monolith/serverless]
- Features needed: [async/real-time/CRUD]
```

### Framework Comparison

#### FastAPI (Python) - Best for:
- High performance async operations
- Type safety requirements
- Auto-generated OpenAPI docs
- ML/AI API backends
- Modern Python projects

**Example Prompt:**
```
Generate FastAPI application for blog API with:
- Posts CRUD
- User authentication
- Comments system
- Tag filtering
- Full async support
```

#### Django REST Framework - Best for:
- Rapid development
- Admin interface needed
- ORM integration
- Large enterprise systems
- Batteries-included approach

**Example Prompt:**
```
Create Django REST API for CRM with:
- Customer management
- Admin interface
- Built-in authentication
- Filtering and pagination
- Comprehensive permissions
```

#### Express.js - Best for:
- Node.js ecosystem
- Real-time features
- Frontend integration
- Microservices
- JavaScript teams

**Example Prompt:**
```
Build Express.js API for chat application:
- WebSocket support
- User presence
- Message history
- File uploads
- JWT auth
```

#### Spring Boot - Best for:
- Enterprise Java projects
- Complex business logic
- Microservices architecture
- Large team coordination
- Mature ecosystem needs

**Example Prompt:**
```
Generate Spring Boot API for banking:
- Account management
- Transaction processing
- Security compliance
- Audit logging
- Distributed transactions
```

## Complete API Structure

### FastAPI Example

**Prompt Copilot:**
```
Generate complete FastAPI project structure with:
- Proper layered architecture
- Dependency injection
- Database models
- Pydantic schemas
- Service layer
- API routers
- Middleware
- Security
- Tests
- Docker setup
```

**Expected Structure:**
```
project/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── core/
│   │   ├── config.py          # Configuration management
│   │   ├── security.py        # JWT, password hashing
│   │   └── database.py        # Database connection
│   ├── api/
│   │   ├── deps.py            # Dependencies
│   │   └── v1/
│   │       ├── endpoints/
│   │       │   ├── users.py
│   │       │   └── items.py
│   │       └── api.py         # Router aggregation
│   ├── models/                # SQLAlchemy models
│   ├── schemas/               # Pydantic schemas
│   ├── services/              # Business logic
│   └── tests/
├── alembic/                   # Database migrations
├── requirements.txt
├── Dockerfile
└── docker-compose.yml
```

### Core Configuration

**Prompt:**
```
Create secure configuration management for FastAPI with:
- Environment variables
- Database connection
- JWT settings
- CORS configuration
- Rate limiting settings
```

**Expected Output:**
```python
# app/core/config.py
from pydantic import BaseSettings, validator
from typing import Optional, List
import secrets

class Settings(BaseSettings):
    # API Settings
    API_V1_STR: str = "/api/v1"
    PROJECT_NAME: str = "My API"
    VERSION: str = "1.0.0"
    
    # Security
    SECRET_KEY: str = secrets.token_urlsafe(32)
    ACCESS_TOKEN_EXPIRE_MINUTES: int = 60 * 24 * 8  # 8 days
    ALGORITHM: str = "HS256"
    
    # Database
    DATABASE_URL: str = "postgresql://user:pass@localhost/dbname"
    
    # Redis Cache
    REDIS_URL: str = "redis://localhost:6379"
    
    # CORS
    BACKEND_CORS_ORIGINS: List[str] = ["http://localhost:3000"]
    
    # Rate Limiting
    RATE_LIMIT_PER_MINUTE: int = 100
    
    class Config:
        env_file = ".env"
        case_sensitive = True

settings = Settings()
```

### Database Setup

**Prompt:**
```
Set up PostgreSQL database connection for FastAPI with:
- SQLAlchemy async engine
- Connection pooling
- Session management
- Health checks
```

**Expected Output:**
```python
# app/core/database.py
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from app.core.config import settings

engine = create_async_engine(
    settings.DATABASE_URL,
    pool_pre_ping=True,
    pool_size=10,
    max_overflow=20,
    echo=False
)

AsyncSessionLocal = sessionmaker(
    engine,
    class_=AsyncSession,
    expire_on_commit=False,
    autocommit=False,
    autoflush=False
)

Base = declarative_base()

async def get_db() -> AsyncSession:
    async with AsyncSessionLocal() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
        finally:
            await session.close()
```

### Security Implementation

**Prompt:**
```
Implement JWT authentication for FastAPI with:
- Token generation
- Token validation
- Password hashing
- Dependency for protected routes
```

**Expected Output:**
```python
# app/core/security.py
from datetime import datetime, timedelta
from typing import Optional
from jose import JWTError, jwt
from passlib.context import CryptContext
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
security = HTTPBearer()

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()
    expire = datetime.utcnow() + (expires_delta or timedelta(minutes=15))
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, settings.SECRET_KEY, algorithm=settings.ALGORITHM)

def verify_token(credentials: HTTPAuthorizationCredentials = Depends(security)):
    try:
        payload = jwt.decode(
            credentials.credentials,
            settings.SECRET_KEY,
            algorithms=[settings.ALGORITHM]
        )
        return payload
    except JWTError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Could not validate credentials"
        )

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)
```

### Database Models

**Prompt:**
```
Create SQLAlchemy models for a blog API with:
- User model (authentication)
- Post model (content)
- Comment model (engagement)
- Proper relationships
- Timestamps
```

**Expected Output:**
```python
# app/models/user.py
from sqlalchemy import Column, Integer, String, Boolean, DateTime
from sqlalchemy.sql import func
from sqlalchemy.orm import relationship
from app.core.database import Base

class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True, nullable=False)
    username = Column(String, unique=True, index=True, nullable=False)
    hashed_password = Column(String, nullable=False)
    full_name = Column(String)
    is_active = Column(Boolean, default=True)
    is_superuser = Column(Boolean, default=False)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
    
    # Relationships
    posts = relationship("Post", back_populates="author")
    comments = relationship("Comment", back_populates="author")

# app/models/post.py
class Post(Base):
    __tablename__ = "posts"
    
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, nullable=False)
    content = Column(Text, nullable=False)
    author_id = Column(Integer, ForeignKey("users.id"))
    is_published = Column(Boolean, default=False)
    created_at = Column(DateTime(timezone=True), server_default=func.now())
    updated_at = Column(DateTime(timezone=True), onupdate=func.now())
    
    # Relationships
    author = relationship("User", back_populates="posts")
    comments = relationship("Comment", back_populates="post")
```

### Pydantic Schemas (Request/Response)

**Prompt:**
```
Create Pydantic schemas for blog API with:
- Input validation
- Response serialization
- Nested relationships
- Custom validators
```

**Expected Output:**
```python
# app/schemas/user.py
from pydantic import BaseModel, EmailStr, validator
from typing import Optional
from datetime import datetime

class UserBase(BaseModel):
    email: EmailStr
    username: str
    full_name: Optional[str] = None

class UserCreate(UserBase):
    password: str
    
    @validator('password')
    def validate_password(cls, v):
        if len(v) < 8:
            raise ValueError('Password must be at least 8 characters')
        if not any(c.isupper() for c in v):
            raise ValueError('Password must contain uppercase letter')
        if not any(c.isdigit() for c in v):
            raise ValueError('Password must contain a digit')
        return v

class UserUpdate(BaseModel):
    email: Optional[EmailStr] = None
    full_name: Optional[str] = None

class UserInDB(UserBase):
    id: int
    is_active: bool
    created_at: datetime
    
    class Config:
        orm_mode = True

class User(UserInDB):
    """Public user representation"""
    pass
```

### API Endpoints

**Prompt:**
```
Create CRUD endpoints for user management with:
- Create user (POST /users)
- Get user by ID (GET /users/{id})
- Get current user (GET /users/me)
- Update user (PUT /users/{id})
- List users with pagination (GET /users)
- Authentication required where appropriate
```

**Expected Output:**
```python
# app/api/v1/endpoints/users.py
from typing import List
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.ext.asyncio import AsyncSession

from app.core.database import get_db
from app.core.security import verify_token
from app.schemas.user import User, UserCreate, UserUpdate
from app.services.user_service import UserService

router = APIRouter()

@router.post("/", response_model=User, status_code=status.HTTP_201_CREATED)
async def create_user(
    user_data: UserCreate,
    db: AsyncSession = Depends(get_db)
):
    """Create a new user"""
    service = UserService(db)
    return await service.create_user(user_data)

@router.get("/me", response_model=User)
async def get_current_user(
    token_data: dict = Depends(verify_token),
    db: AsyncSession = Depends(get_db)
):
    """Get current authenticated user"""
    service = UserService(db)
    user = await service.get_user_by_email(token_data["sub"])
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user

@router.get("/{user_id}", response_model=User)
async def get_user(
    user_id: int,
    db: AsyncSession = Depends(get_db),
    _: dict = Depends(verify_token)
):
    """Get user by ID"""
    service = UserService(db)
    user = await service.get_user(user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user

@router.get("/", response_model=List[User])
async def list_users(
    skip: int = 0,
    limit: int = 100,
    db: AsyncSession = Depends(get_db),
    _: dict = Depends(verify_token)
):
    """List users with pagination"""
    service = UserService(db)
    return await service.get_users(skip=skip, limit=limit)

@router.put("/{user_id}", response_model=User)
async def update_user(
    user_id: int,
    user_update: UserUpdate,
    db: AsyncSession = Depends(get_db),
    _: dict = Depends(verify_token)
):
    """Update user"""
    service = UserService(db)
    return await service.update_user(user_id, user_update)
```

### Service Layer (Business Logic)

**Prompt:**
```
Create service layer for user operations with:
- Business logic separation
- Database operations
- Error handling
- Validation
```

**Expected Output:**
```python
# app/services/user_service.py
from typing import Optional, List
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select
from fastapi import HTTPException, status

from app.models.user import User
from app.schemas.user import UserCreate, UserUpdate
from app.core.security import hash_password

class UserService:
    def __init__(self, db: AsyncSession):
        self.db = db
    
    async def get_user(self, user_id: int) -> Optional[User]:
        result = await self.db.execute(
            select(User).where(User.id == user_id)
        )
        return result.scalar_one_or_none()
    
    async def get_user_by_email(self, email: str) -> Optional[User]:
        result = await self.db.execute(
            select(User).where(User.email == email)
        )
        return result.scalar_one_or_none()
    
    async def create_user(self, user_data: UserCreate) -> User:
        # Check if user exists
        existing_user = await self.get_user_by_email(user_data.email)
        if existing_user:
            raise HTTPException(
                status_code=status.HTTP_400_BAD_REQUEST,
                detail="Email already registered"
            )
        
        # Create user
        db_user = User(
            email=user_data.email,
            username=user_data.username,
            full_name=user_data.full_name,
            hashed_password=hash_password(user_data.password)
        )
        self.db.add(db_user)
        await self.db.commit()
        await self.db.refresh(db_user)
        return db_user
    
    async def update_user(self, user_id: int, user_update: UserUpdate) -> User:
        user = await self.get_user(user_id)
        if not user:
            raise HTTPException(status_code=404, detail="User not found")
        
        update_data = user_update.dict(exclude_unset=True)
        for field, value in update_data.items():
            setattr(user, field, value)
        
        await self.db.commit()
        await self.db.refresh(user)
        return user
    
    async def get_users(self, skip: int = 0, limit: int = 100) -> List[User]:
        result = await self.db.execute(
            select(User).offset(skip).limit(limit)
        )
        return result.scalars().all()
```

### Middleware & Error Handling

**Prompt:**
```
Add middleware for FastAPI with:
- Request logging
- Correlation IDs
- Rate limiting
- Global error handling
```

**Expected Output:**
```python
# app/main.py (middleware section)
import uuid
import time
import logging
from fastapi import FastAPI, Request, status
from fastapi.responses import JSONResponse
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(title=settings.PROJECT_NAME, version=settings.VERSION)

# CORS middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=settings.BACKEND_CORS_ORIGINS,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.middleware("http")
async def add_correlation_id(request: Request, call_next):
    correlation_id = str(uuid.uuid4())
    request.state.correlation_id = correlation_id
    
    response = await call_next(request)
    response.headers["X-Correlation-ID"] = correlation_id
    return response

@app.middleware("http")
async def log_requests(request: Request, call_next):
    start_time = time.time()
    
    response = await call_next(request)
    
    process_time = time.time() - start_time
    logging.info(
        f"{request.method} {request.url.path} "
        f"completed in {process_time:.2f}s "
        f"with status {response.status_code}"
    )
    
    response.headers["X-Process-Time"] = str(process_time)
    return response

@app.exception_handler(Exception)
async def global_exception_handler(request: Request, exc: Exception):
    correlation_id = getattr(request.state, 'correlation_id', 'unknown')
    
    logging.error(
        f"Unhandled exception: {exc}",
        extra={"correlation_id": correlation_id}
    )
    
    return JSONResponse(
        status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
        content={
            "detail": "Internal server error",
            "correlation_id": correlation_id
        }
    )
```

## Testing

**Prompt:**
```
Generate comprehensive tests for the user API including:
- Unit tests for service layer
- Integration tests for endpoints
- Test fixtures
- Mock database
```

**Expected Output:**
```python
# app/tests/conftest.py
import pytest
from fastapi.testclient import TestClient
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.main import app
from app.core.database import Base, get_db

SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"
engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
TestingSessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

@pytest.fixture
def db():
    Base.metadata.create_all(bind=engine)
    db = TestingSessionLocal()
    try:
        yield db
    finally:
        db.close()
        Base.metadata.drop_all(bind=engine)

@pytest.fixture
def client(db):
    def override_get_db():
        try:
            yield db
        finally:
            db.close()
    
    app.dependency_overrides[get_db] = override_get_db
    return TestClient(app)

# app/tests/test_users.py
def test_create_user(client):
    response = client.post("/api/v1/users/", json={
        "email": "test@example.com",
        "username": "testuser",
        "password": "SecurePass123",
        "full_name": "Test User"
    })
    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "test@example.com"
    assert "id" in data

def test_create_duplicate_user(client):
    # Create first user
    client.post("/api/v1/users/", json={
        "email": "test@example.com",
        "username": "testuser",
        "password": "SecurePass123"
    })
    
    # Try to create duplicate
    response = client.post("/api/v1/users/", json={
        "email": "test@example.com",
        "username": "testuser2",
        "password": "SecurePass123"
    })
    assert response.status_code == 400
    assert "already registered" in response.json()["detail"]
```

## Deployment

### Docker

**Prompt:**
```
Create production-ready Dockerfile for FastAPI with:
- Multi-stage build
- Security best practices
- Health checks
- Non-root user
```

**Expected Output:**
```dockerfile
# Dockerfile
FROM python:3.11-slim as builder

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /app/wheels -r requirements.txt

FROM python:3.11-slim

WORKDIR /app

# Create non-root user
RUN addgroup --system app && adduser --system --group app

# Install dependencies
COPY --from=builder /app/wheels /wheels
COPY --from=builder /app/requirements.txt .
RUN pip install --no-cache /wheels/*

# Copy application
COPY --chown=app:app . .

# Switch to non-root user
USER app

# Health check
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import requests; requests.get('http://localhost:8000/health')"

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Docker Compose

**Prompt:**
```
Create docker-compose.yml with:
- API service
- PostgreSQL database
- Redis cache
- Development configuration
```

**Expected Output:**
```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/app
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
    volumes:
      - ./app:/app/app
    restart: unless-stopped

  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=app
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    restart: unless-stopped

volumes:
  postgres_data:
```

## Quick Reference Commands

### Create FastAPI Application
```
Generate production-ready FastAPI application for [domain] with:
- Complete CRUD operations
- JWT authentication
- PostgreSQL database
- Input validation
- Error handling
- Tests
- Docker setup
```

### Create Express.js Application
```
Create Express.js REST API for [domain] with TypeScript:
- RESTful endpoints
- JWT auth
- Prisma ORM
- Validation
- Error handling
- Tests
```

### Create Django REST API
```
Generate Django REST Framework API for [domain]:
- DRF serializers
- ViewSets
- Authentication
- Permissions
- Admin interface
```

### Add Feature to Existing API
```
Add [feature] endpoint to existing API:
- Model/Schema definition
- CRUD operations
- Validation
- Tests
- Documentation
```

## Related Patterns

- `security-scan.md` - Security validation for API
- `test-harness.md` - Comprehensive testing setup
- `docker-optimize.md` - Container optimization
- `k8s-manifest.md` - Kubernetes deployment
- `db-migrate.md` - Database migrations

## Best Practices Checklist

- [ ] Input validation on all endpoints
- [ ] Proper error handling and status codes
- [ ] Authentication/authorization implemented
- [ ] Rate limiting configured
- [ ] API versioning (e.g., /api/v1/)
- [ ] Comprehensive tests (80%+ coverage)
- [ ] OpenAPI/Swagger documentation
- [ ] Logging and monitoring
- [ ] Docker containerization
- [ ] Environment-based configuration
- [ ] Database migrations
- [ ] CORS properly configured
- [ ] Security headers implemented
