# Backend Development Guide

## Project Overview
- **Project Name:** [Your Project Name]
- **Description:** [Brief description of what this backend service does]
- **Tech Stack:** Node.js, Express, PostgreSQL, Redis
- **API Version:** v1
- **Base URL:** `https://api.example.com/v1`

---

## Architecture

### System Architecture
```
Client → Load Balancer → API Gateway → Microservices → Database
                                     ↓
                                  Cache (Redis)
                                     ↓
                                  Message Queue
```

### Tech Stack Details
- **Runtime:** Node.js 20.x
- **Framework:** Express.js 4.x
- **Database:** PostgreSQL 15
- **ORM:** Prisma / TypeORM
- **Cache:** Redis 7.x
- **Authentication:** JWT (JSON Web Tokens)
- **API Documentation:** Swagger / OpenAPI 3.0
- **Testing:** Jest, Supertest
- **Logging:** Winston, Morgan
- **Process Manager:** PM2

---

## Project Structure

```
backend/
├── src/
│   ├── controllers/       # Request handlers
│   ├── services/          # Business logic
│   ├── models/            # Database models
│   ├── routes/            # API routes
│   ├── middleware/        # Custom middleware
│   ├── utils/             # Helper functions
│   ├── config/            # Configuration files
│   ├── validators/        # Input validation schemas
│   └── types/             # TypeScript types
├── tests/
│   ├── unit/              # Unit tests
│   ├── integration/       # Integration tests
│   └── e2e/               # End-to-end tests
├── docs/                  # API documentation
├── scripts/               # Utility scripts
├── .env.example           # Environment variables template
├── .gitignore
├── package.json
├── tsconfig.json
└── README.md
```

---

## Setup Instructions

### Prerequisites
- Node.js >= 20.x
- PostgreSQL >= 15
- Redis >= 7.x
- npm or yarn or pnpm

### Installation Steps

1. **Clone the repository**
```bash
git clone https://github.com/your-org/your-project.git
cd your-project/backend
```

2. **Install dependencies**
```bash
npm install
# or
yarn install
# or
pnpm install
```

3. **Environment Setup**
```bash
cp .env.example .env
# Edit .env with your configuration
```

4. **Database Setup**
```bash
# Run migrations
npm run migrate

# Seed database (optional)
npm run seed
```

5. **Start Development Server**
```bash
npm run dev
```

Server will run at: `http://localhost:3000`

---

## Environment Variables

```bash
# Server Configuration
NODE_ENV=development
PORT=3000
API_VERSION=v1

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
DB_HOST=localhost
DB_PORT=5432
DB_NAME=your_database
DB_USER=your_username
DB_PASSWORD=your_password

# Redis
REDIS_URL=redis://localhost:6379
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=

# JWT
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
JWT_EXPIRES_IN=7d
REFRESH_TOKEN_SECRET=your-refresh-token-secret
REFRESH_TOKEN_EXPIRES_IN=30d

# Email Service (Optional)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
EMAIL_FROM=noreply@example.com

# File Upload
UPLOAD_DIR=./uploads
MAX_FILE_SIZE=10485760  # 10MB in bytes

# External APIs
PAYMENT_API_KEY=your-payment-api-key
PAYMENT_API_SECRET=your-payment-api-secret

# Logging
LOG_LEVEL=info
LOG_FILE=./logs/app.log

# CORS
CORS_ORIGIN=http://localhost:5173,http://localhost:3001
```

---

## API Endpoints

### Authentication

#### Register User
```http
POST /api/v1/auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123!",
  "name": "John Doe"
}

Response: 201 Created
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "name": "John Doe"
    },
    "token": "jwt-token-here"
  }
}
```

#### Login
```http
POST /api/v1/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePass123!"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "user": { ... },
    "token": "jwt-token-here",
    "refreshToken": "refresh-token-here"
  }
}
```

#### Get Current User
```http
GET /api/v1/auth/me
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "user"
  }
}
```

### Users

#### Get All Users (Admin only)
```http
GET /api/v1/users?page=1&limit=10&sort=createdAt:desc
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "email": "user@example.com",
      "name": "John Doe",
      "createdAt": "2024-01-01T00:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "totalPages": 10
  }
}
```

#### Get User by ID
```http
GET /api/v1/users/:id
Authorization: Bearer {token}

Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "user",
    "createdAt": "2024-01-01T00:00:00Z",
    "updatedAt": "2024-01-01T00:00:00Z"
  }
}
```

#### Update User
```http
PUT /api/v1/users/:id
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Jane Doe",
  "bio": "Updated bio"
}

Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Jane Doe",
    "bio": "Updated bio"
  }
}
```

#### Delete User
```http
DELETE /api/v1/users/:id
Authorization: Bearer {token}

Response: 204 No Content
```

### Posts/Content

#### Create Post
```http
POST /api/v1/posts
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "My First Post",
  "content": "This is the content of my post",
  "category": "technology",
  "tags": ["nodejs", "api"]
}

Response: 201 Created
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "My First Post",
    "slug": "my-first-post",
    "content": "This is the content...",
    "authorId": "uuid",
    "createdAt": "2024-01-01T00:00:00Z"
  }
}
```

#### Get All Posts
```http
GET /api/v1/posts?page=1&limit=10&category=technology

Response: 200 OK
{
  "success": true,
  "data": [
    {
      "id": "uuid",
      "title": "My First Post",
      "excerpt": "This is the content...",
      "author": {
        "id": "uuid",
        "name": "John Doe"
      },
      "createdAt": "2024-01-01T00:00:00Z"
    }
  ],
  "pagination": { ... }
}
```

#### Get Post by ID
```http
GET /api/v1/posts/:id

Response: 200 OK
{
  "success": true,
  "data": {
    "id": "uuid",
    "title": "My First Post",
    "content": "Full content here...",
    "author": { ... },
    "tags": ["nodejs", "api"],
    "createdAt": "2024-01-01T00:00:00Z",
    "viewCount": 100
  }
}
```

---

## Database Schema

### Users Table
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255) NOT NULL,
  role VARCHAR(50) DEFAULT 'user',
  avatar_url VARCHAR(500),
  bio TEXT,
  is_verified BOOLEAN DEFAULT false,
  is_active BOOLEAN DEFAULT true,
  last_login TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);
```

### Posts Table
```sql
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  title VARCHAR(255) NOT NULL,
  slug VARCHAR(255) UNIQUE NOT NULL,
  content TEXT NOT NULL,
  excerpt TEXT,
  author_id UUID REFERENCES users(id) ON DELETE CASCADE,
  category VARCHAR(100),
  status VARCHAR(50) DEFAULT 'draft',
  view_count INTEGER DEFAULT 0,
  published_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_posts_author_id ON posts(author_id);
CREATE INDEX idx_posts_slug ON posts(slug);
CREATE INDEX idx_posts_created_at ON posts(created_at);
```

---

## Error Handling

### Error Response Format
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human readable error message",
    "details": {}
  }
}
```

### HTTP Status Codes
- `200 OK` - Success
- `201 Created` - Resource created
- `204 No Content` - Success with no response body
- `400 Bad Request` - Invalid input
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `409 Conflict` - Resource already exists
- `422 Unprocessable Entity` - Validation error
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Server error

### Common Error Codes
- `AUTH_001` - Invalid credentials
- `AUTH_002` - Token expired
- `AUTH_003` - Invalid token
- `USER_001` - User not found
- `USER_002` - Email already exists
- `VALID_001` - Validation error
- `PERM_001` - Insufficient permissions

---

## Authentication & Authorization

### JWT Token Structure
```json
{
  "userId": "uuid",
  "email": "user@example.com",
  "role": "user",
  "iat": 1234567890,
  "exp": 1234567890
}
```

### Protected Routes
Add authentication middleware:
```javascript
router.get('/protected', authenticateToken, (req, res) => {
  // req.user contains decoded JWT payload
  res.json({ user: req.user });
});
```

### Role-Based Access Control
```javascript
router.delete('/admin-only', 
  authenticateToken, 
  requireRole(['admin']), 
  (req, res) => {
    // Only admins can access
  }
);
```

---

## Middleware

### Request Logging
All requests are logged with:
- Timestamp
- HTTP Method
- URL
- Status Code
- Response Time
- IP Address

### Rate Limiting
- Anonymous: 100 requests per 15 minutes
- Authenticated: 1000 requests per 15 minutes
- Admin: Unlimited

### Input Validation
All inputs are validated using schemas:
```javascript
const createUserSchema = {
  email: { type: 'string', format: 'email', required: true },
  password: { type: 'string', minLength: 8, required: true },
  name: { type: 'string', minLength: 2, maxLength: 100, required: true }
};
```

### CORS Configuration
```javascript
{
  origin: process.env.CORS_ORIGIN.split(','),
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization']
}
```

---

## Testing

### Run Tests
```bash
# Run all tests
npm test

# Run unit tests only
npm run test:unit

# Run integration tests
npm run test:integration

# Run with coverage
npm run test:coverage
```

### Test Structure
```javascript
describe('User API', () => {
  describe('POST /api/v1/users', () => {
    it('should create a new user', async () => {
      const response = await request(app)
        .post('/api/v1/users')
        .send({
          email: 'test@example.com',
          password: 'password123',
          name: 'Test User'
        });
      
      expect(response.status).toBe(201);
      expect(response.body.success).toBe(true);
    });
  });
});
```

---

## Deployment

### Production Checklist
- [ ] Environment variables configured
- [ ] Database migrations run
- [ ] SSL/TLS certificates installed
- [ ] Rate limiting configured
- [ ] Logging and monitoring setup
- [ ] Backup strategy implemented
- [ ] CI/CD pipeline configured
- [ ] Load balancer configured
- [ ] Health check endpoint working

### Docker Deployment
```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

### Health Check Endpoint
```http
GET /health

Response: 200 OK
{
  "status": "ok",
  "timestamp": "2024-01-01T00:00:00Z",
  "uptime": 12345,
  "database": "connected",
  "redis": "connected"
}
```

---

## Performance Optimization

### Database Optimization
- Use indexes on frequently queried columns
- Implement query result caching with Redis
- Use connection pooling
- Optimize N+1 queries with eager loading

### API Optimization
- Implement pagination for large datasets
- Use compression (gzip)
- Cache static responses
- Implement CDN for static assets

### Security Best Practices
- Use HTTPS only
- Implement rate limiting
- Sanitize all user inputs
- Use parameterized queries (prevent SQL injection)
- Hash passwords with bcrypt (cost factor >= 12)
- Implement CSRF protection
- Set security headers (Helmet.js)
- Regular security audits

---

## Logging

### Log Levels
- `error` - Error messages
- `warn` - Warning messages
- `info` - Informational messages
- `http` - HTTP request logs
- `debug` - Debug messages

### Log Format
```json
{
  "timestamp": "2024-01-01T00:00:00Z",
  "level": "info",
  "message": "User logged in",
  "userId": "uuid",
  "ip": "192.168.1.1",
  "userAgent": "Mozilla/5.0..."
}
```

---

## Monitoring

### Key Metrics to Monitor
- Response time (p50, p95, p99)
- Error rate
- Request rate
- Database query performance
- Memory usage
- CPU usage
- Disk I/O

### Recommended Tools
- Application: New Relic, DataDog, Sentry
- Infrastructure: Prometheus + Grafana
- Logging: ELK Stack (Elasticsearch, Logstash, Kibana)

---

## Troubleshooting

### Common Issues

#### Database Connection Failed
```bash
# Check database is running
pg_isready -h localhost -p 5432

# Check credentials in .env
# Verify firewall rules
```

#### Redis Connection Failed
```bash
# Check Redis is running
redis-cli ping

# Should return: PONG
```

#### JWT Token Invalid
- Check JWT_SECRET matches in .env
- Verify token hasn't expired
- Check Authorization header format: `Bearer <token>`

---

## Contributing

### Git Workflow
1. Create feature branch: `git checkout -b feature/your-feature`
2. Make changes and commit: `git commit -am "Add feature"`
3. Push to branch: `git push origin feature/your-feature`
4. Create Pull Request

### Code Style
- Use ESLint and Prettier
- Follow Airbnb style guide
- Write meaningful commit messages
- Add JSDoc comments for functions

### Pull Request Template
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
```

---

## Resources

### Documentation
- Express.js: https://expressjs.com/
- PostgreSQL: https://www.postgresql.org/docs/
- Prisma: https://www.prisma.io/docs/
- JWT: https://jwt.io/

### Learning Resources
- Node.js Best Practices: https://github.com/goldbergyoni/nodebestpractices
- API Design Guide: https://github.com/microsoft/api-guidelines

---

## Contact & Support

- **Technical Lead:** tech-lead@example.com
- **DevOps Team:** devops@example.com
- **Slack Channel:** #backend-support
- **Documentation:** https://docs.example.com

---

**Last Updated:** 2024-01-20  
**Version:** 1.0.0