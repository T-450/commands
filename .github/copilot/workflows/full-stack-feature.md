# Full Stack Feature Workflow - GitHub Copilot Instructions

## Overview

This workflow orchestrates comprehensive full-stack feature implementation across backend, frontend, mobile, and API layers. Each phase builds upon previous work to create a cohesive multi-platform solution with coordinated development across all layers.

## When to Use

**Prompt GitHub Copilot with:**
- "Implement user authentication full-stack feature across all platforms"
- "Build shopping cart feature using full-stack-feature workflow"
- "Create real-time chat feature across backend, web, mobile, and GraphQL API"
- "Develop payment processing full-stack feature with complete integration"

## Workflow Phases

### Phase 1: Architecture and API Design

#### Step 1: Backend Architecture

**Prompt GitHub Copilot:**
"Design backend architecture for [feature]. Include service boundaries, database schema, data models, API structure, and technology stack recommendations. Consider scalability, security, and maintainability."

Deliverables:
- **Service Architecture**: Microservices vs. monolith decision
- **Database Schema**: Tables, relationships, indexes
- **Data Models**: Entity definitions, validation rules
- **API Structure**: RESTful endpoints or GraphQL schema
- **Technology Stack**: Language, framework, database choices
- **Security Design**: Authentication, authorization, data protection
- **Caching Strategy**: What to cache, cache invalidation
- **Scalability Plan**: How to scale each component

Example backend design:
```
Service: User Authentication
Database: PostgreSQL
Framework: FastAPI (Python) or Express (Node.js)
APIs:
  - POST /api/v1/auth/register
  - POST /api/v1/auth/login
  - POST /api/v1/auth/refresh
  - POST /api/v1/auth/logout
  - GET /api/v1/auth/me
Cache: Redis for session storage
Queue: RabbitMQ for email notifications
```

#### Step 2: GraphQL API Design (If Applicable)

**Prompt GitHub Copilot:**
"Design GraphQL schema and resolvers for [feature] building on the backend architecture. Include types, queries, mutations, subscriptions, and federation strategy if using Apollo Federation."

Deliverables:
- **GraphQL Schema**: Type definitions, interfaces, unions
- **Queries**: Data fetching operations
- **Mutations**: Data modification operations
- **Subscriptions**: Real-time updates
- **Resolvers**: Implementation strategy
- **Federation**: If using microservices with GraphQL
- **DataLoader**: Batching and caching strategy
- **Authentication**: How to protect resolvers

Example GraphQL design:
```graphql
type User {
  id: ID!
  email: String!
  name: String!
  avatar: String
  createdAt: DateTime!
}

type AuthPayload {
  token: String!
  refreshToken: String!
  user: User!
}

type Query {
  me: User
  user(id: ID!): User
}

type Mutation {
  register(email: String!, password: String!, name: String!): AuthPayload!
  login(email: String!, password: String!): AuthPayload!
  refreshToken(refreshToken: String!): AuthPayload!
  logout: Boolean!
}

type Subscription {
  userUpdated(id: ID!): User!
}
```

### Phase 2: Implementation

#### Step 3: Frontend Development

**Prompt GitHub Copilot:**
"Implement web frontend for [feature] using the API design: [paste API/GraphQL design]. Create responsive UI with React/Vue/Angular, implement state management (Redux/Zustand/Pinia), integrate with API, and ensure accessibility (WCAG 2.1 AA)."

Deliverables:
- **UI Components**: Reusable, accessible React/Vue/Angular components
- **State Management**: Redux/Zustand/Pinia/Vuex setup
- **API Integration**: Axios/Fetch/Apollo Client with error handling
- **Routing**: React Router/Vue Router/Angular Router
- **Forms**: Validation, error handling, user feedback
- **Responsive Design**: Mobile, tablet, desktop breakpoints
- **Accessibility**: ARIA labels, keyboard navigation, screen reader support
- **Performance**: Code splitting, lazy loading, memoization
- **Testing**: Component tests, integration tests

Frontend tech stack examples:
- React + TypeScript + Redux Toolkit + React Query
- Vue 3 + TypeScript + Pinia + Axios
- Angular + RxJS + NgRx + HttpClient

#### Step 4: Mobile Development

**Prompt GitHub Copilot:**
"Implement mobile app features for [feature] using the same API contract as web. Build with React Native/Flutter, ensure UI/UX consistency with web frontend, implement offline support with local storage, and add native integrations (push notifications, biometric auth, camera)."

Deliverables:
- **Cross-Platform UI**: React Native/Flutter components matching web design
- **Native Features**:
  - Push notifications (Firebase/OneSignal)
  - Biometric authentication (Face ID/Touch ID/Fingerprint)
  - Camera/photo gallery access
  - Location services
  - Native storage
- **Offline Support**: 
  - Local database (SQLite/Realm/Async Storage)
  - Sync strategies
  - Conflict resolution
- **State Management**: Redux/MobX/Provider/Riverpod
- **API Integration**: Same endpoints as web
- **Platform-Specific**: iOS and Android optimizations
- **Performance**: Optimized for mobile devices
- **Testing**: Unit tests, integration tests, E2E tests with Detox/Maestro

Mobile tech stack examples:
- React Native + Redux + React Navigation + Async Storage
- Flutter + Provider/Riverpod + Dio + Sqflite
- Expo for rapid React Native development

### Phase 3: Quality Assurance

#### Step 5: Comprehensive Testing

**Prompt GitHub Copilot:**
"Create comprehensive test suites for [feature] covering:
- Backend APIs: [list endpoints]
- Frontend components: [list components]
- Mobile app features: [list features]
- Integration tests across all platforms
Include unit tests, integration tests, e2e tests, and test documentation."

Testing coverage:

**Backend Tests**:
- Unit tests for business logic (80%+ coverage)
- Integration tests for API endpoints
- Database tests for data access layer
- Authentication/authorization tests
- Performance tests for critical paths
- Contract tests for API consumers

**Frontend Tests**:
- Component unit tests (Jest/Vitest + Testing Library)
- Integration tests for user flows
- E2E tests (Cypress/Playwright)
- Accessibility tests (axe-core)
- Visual regression tests (Chromatic/Percy)
- Performance tests (Lighthouse CI)

**Mobile Tests**:
- Unit tests for business logic
- Component tests (Jest + Testing Library)
- Integration tests
- E2E tests (Detox/Maestro/Appium)
- Platform-specific tests (iOS/Android)
- Offline functionality tests

**Integration Tests**:
- API contract tests (Pact)
- Cross-platform data flow tests
- Real-time sync tests
- Error handling across platforms

#### Step 6: Security Review

**Prompt GitHub Copilot:**
"Audit security across all implementations for [feature]. Check:
- API security (authentication, authorization, rate limiting)
- Frontend vulnerabilities (XSS, CSRF, injection)
- Mobile app security (secure storage, API key protection)
- Data encryption in transit and at rest
Provide security report with remediation steps."

Security checklist:

**API Security**:
- Authentication implementation (JWT, OAuth2)
- Authorization checks on all endpoints
- Input validation and sanitization
- Rate limiting and throttling
- SQL injection prevention
- CORS configuration
- Security headers (HSTS, CSP, X-Frame-Options)
- Secrets management

**Frontend Security**:
- XSS prevention (output encoding, CSP)
- CSRF protection
- Secure cookie configuration
- Content Security Policy
- Dependency vulnerability scanning
- Secure local storage usage
- API key protection

**Mobile Security**:
- Secure storage (Keychain/Keystore)
- Certificate pinning
- Biometric authentication
- Jailbreak/root detection
- Code obfuscation
- Secure communication (HTTPS only)
- API key protection

### Phase 4: Optimization and Deployment

#### Step 7: Performance Optimization

**Prompt GitHub Copilot:**
"Optimize performance across all platforms for [feature]. Focus on:
- API response times and database query optimization
- Frontend bundle size, lazy loading, and caching
- Mobile app performance, startup time, and memory usage
Provide performance improvements, caching strategies, and optimization report."

Performance targets:

**Backend**:
- API response time < 200ms (P95)
- Database queries < 100ms
- Implement caching (Redis)
- Optimize N+1 queries
- Connection pooling
- Async processing for heavy tasks

**Frontend**:
- Initial load < 3s (3G)
- Time to Interactive < 5s
- Bundle size < 200KB gzipped
- Code splitting per route
- Image optimization
- Lazy loading for non-critical resources
- Service worker for caching

**Mobile**:
- App startup < 2s
- Smooth 60fps animations
- Memory usage < 100MB baseline
- Battery-efficient background tasks
- Optimized images and assets
- Efficient local data storage

#### Step 8: Deployment Preparation

**Prompt GitHub Copilot:**
"Prepare deployment for all components of [feature]. Include:
- CI/CD pipelines for backend, frontend, and mobile
- Containerization (Docker)
- Kubernetes manifests or cloud deployment configs
- Monitoring and observability setup
- Rollout strategy (blue-green, canary)
Provide deployment configurations, monitoring dashboards, and rollback procedures."

Deployment deliverables:

**CI/CD Pipeline**:
- Automated testing in pipeline
- Build and containerization
- Security scanning
- Multi-environment deployment (dev, staging, prod)
- Approval gates for production
- Automated rollback on failure

**Infrastructure**:
- Docker images for backend services
- Kubernetes manifests or cloud configs
- Database migrations
- Environment configuration
- Secrets management
- Load balancer configuration

**Monitoring**:
- Application Performance Monitoring (APM)
- Error tracking (Sentry)
- Logging (ELK/Loki)
- Metrics (Prometheus + Grafana)
- Uptime monitoring
- User analytics

**Mobile Deployment**:
- App Store submission (iOS)
- Google Play submission (Android)
- Beta testing (TestFlight/Firebase App Distribution)
- Phased rollout
- Crash reporting (Firebase Crashlytics)

## Coordination Best Practices

1. **API Contract First**: Design and agree on API contracts before implementation
2. **Shared Documentation**: Maintain single source of truth for all platforms
3. **Regular Sync**: Coordinate between frontend, backend, and mobile teams
4. **Integration Points**: Clearly define and test integration points
5. **Consistent Errors**: Use same error codes and messages across platforms
6. **Unified Design**: Use design system for consistent UI/UX
7. **Version Together**: Keep platform versions aligned
8. **Cross-Platform Testing**: Test integration between all platforms

## Example Complete Feature Implementation

**Comprehensive Prompt:**
"Implement complete user authentication feature across all platforms:

**Backend** (FastAPI/Node.js):
- User registration with email verification
- Login with JWT tokens
- Refresh token mechanism
- Password reset flow
- User profile management
- Session management

**GraphQL API**:
- User queries and mutations
- Real-time user status subscriptions
- Authentication directive for protected resolvers

**Web Frontend** (React):
- Registration and login forms
- Password reset flow
- User profile page
- Session management
- Protected routes
- Remember me functionality

**Mobile App** (React Native):
- Same features as web
- Biometric authentication option
- Push notification registration
- Offline mode support
- Deep linking for password reset

**Testing**: Comprehensive test coverage across all platforms
**Security**: Full security audit and fixes
**Performance**: Optimization for all platforms
**Deployment**: Complete CI/CD and monitoring setup"

## Validation Checklist

- [ ] Backend API fully implemented and tested
- [ ] Frontend UI complete and responsive
- [ ] Mobile app working on iOS and Android
- [ ] GraphQL API (if applicable) fully functional
- [ ] All tests passing (80%+ coverage)
- [ ] Security audit completed and issues fixed
- [ ] Performance benchmarks met
- [ ] Documentation complete
- [ ] CI/CD pipelines working
- [ ] Monitoring and alerting configured
- [ ] Deployment to staging successful
- [ ] User acceptance testing passed
- [ ] Ready for production deployment

## Related Patterns

- `feature-development.md` - Single-platform feature development
- `multi-platform.md` - Coordinated multi-platform development
- `api-scaffold.md` (tool) - API boilerplate generation
- `graphql-architect.md` (tool) - GraphQL schema design
- `mobile-app-scaffold.md` (tool) - Mobile app structure
- `component-library.md` (tool) - Shared component library
- `integration-test.md` (tool) - Cross-platform integration tests
- `security-hardening.md` - Security-first development
- `performance-optimization.md` - Performance tuning
