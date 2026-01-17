# Multi-Platform Workflow - GitHub Copilot Instructions

## Overview

This workflow builds the same feature across multiple platforms (web, mobile, API) in parallel while ensuring consistency in functionality, design, and API contracts. It coordinates development across platforms to deliver a unified multi-platform experience.

## When to Use

**Prompt GitHub Copilot with:**
- "Build user profile feature across web, mobile, and API using multi-platform workflow"
- "Implement shopping cart for web and mobile platforms with consistent API"
- "Create notification system across all platforms with multi-platform approach"
- "Develop search functionality for web, iOS, Android, and API simultaneously"

## Parallel Development Strategy

This workflow executes platform implementations in parallel while maintaining consistency through shared API contracts and design specifications.

### Phase 1: Shared Specifications

Before parallel development, establish shared contracts:

**Prompt GitHub Copilot:**
"Define shared API contracts and design specifications for [feature] that will be implemented across web, mobile, and backend platforms. Include API endpoints, data models, business logic, and UX patterns."

Specifications should include:
- **API Contracts**: Endpoints, request/response formats, error codes
- **Data Models**: Shared data structures across platforms
- **Business Logic**: Core logic that must be consistent
- **UX Patterns**: Interaction patterns, user flows
- **Design System**: Colors, typography, spacing, components
- **Error Handling**: Consistent error messages and handling
- **Authentication**: Auth flows across platforms
- **Offline Behavior**: How each platform handles offline mode

### Phase 2: Parallel Implementation

Execute these three workstreams in parallel:

#### Web Implementation

**Prompt GitHub Copilot:**
"Implement web frontend for [feature] using the API contract: [paste API spec]. Create responsive UI with React/Vue/Angular, implement state management, integrate with API, and ensure accessibility."

Web deliverables:
- **UI Components**: Reusable, accessible components
- **State Management**: Redux/Zustand/Pinia setup
- **API Integration**: API client with error handling
- **Responsive Design**: Mobile, tablet, desktop layouts
- **Accessibility**: WCAG 2.1 AA compliance
- **Performance**: Code splitting, lazy loading
- **Testing**: Component tests, integration tests
- **Progressive Web App**: Service workers, offline support

Example technologies:
- React + TypeScript + Tailwind CSS
- Vue 3 + Pinia + Vuetify
- Angular + RxJS + Material UI

#### Mobile Implementation

**Prompt GitHub Copilot:**
"Implement mobile app feature for [feature] using the same API contract as web. Build with React Native/Flutter, ensure consistency with web UI, implement offline support, and add native integrations (push notifications, camera, etc.)."

Mobile deliverables:
- **Cross-Platform UI**: React Native/Flutter components
- **Native Features**: Push notifications, biometric auth, camera
- **Offline Support**: Local storage, sync strategies
- **State Management**: Consistent with web approach
- **API Integration**: Same API contract as web
- **Performance**: Optimized for mobile devices
- **Platform-Specific**: iOS and Android optimizations
- **Testing**: Unit tests, integration tests, E2E tests

Example technologies:
- React Native + Redux + Native Base
- Flutter + Provider/Riverpod + Material Design
- Expo for rapid development

#### API Documentation

**Prompt GitHub Copilot:**
"Create comprehensive API documentation for [feature] that both web and mobile teams are implementing. Include OpenAPI/Swagger specs, authentication details, example requests/responses, error codes, and rate limiting."

API documentation deliverables:
- **OpenAPI Specification**: Complete API definition
- **Authentication Guide**: How to authenticate requests
- **Endpoint Documentation**: Each endpoint documented
- **Code Examples**: Multiple languages (JavaScript, Dart, Swift, Kotlin)
- **Error Reference**: All error codes and meanings
- **Rate Limiting**: Limits and best practices
- **Webhooks**: If applicable
- **Changelog**: API version history

### Phase 3: Ensure Cross-Platform Consistency

**Prompt GitHub Copilot:**
"Review and ensure consistency across web, mobile, and API implementations for [feature]. Verify API contracts are honored, UX patterns match, error handling is consistent, and data models align."

Consistency checks:
- **API Contract Compliance**: All platforms use API correctly
- **Data Model Alignment**: Same data structures across platforms
- **UX Consistency**: Similar user flows and interactions
- **Error Handling**: Consistent error messages and recovery
- **Business Logic**: Core logic behaves identically
- **Performance**: Similar performance characteristics
- **Accessibility**: Consistent accessibility across platforms
- **Branding**: Visual consistency in design

### Phase 4: Integration Testing

**Prompt GitHub Copilot:**
"Create integration tests that verify [feature] works correctly across all platforms. Test API contracts, cross-platform data flow, and end-to-end user journeys."

Integration test coverage:
- **API Contract Tests**: Verify API adheres to spec
- **Cross-Platform Flows**: User actions across platforms
- **Data Synchronization**: Data stays consistent
- **Real-Device Testing**: Test on actual devices
- **Network Conditions**: Test offline, slow networks
- **Edge Cases**: Handle platform-specific edge cases

## Platform-Specific Considerations

### Web Platform
- **Browser Compatibility**: Chrome, Firefox, Safari, Edge
- **Responsive Design**: Mobile, tablet, desktop breakpoints
- **Performance**: Bundle size, lazy loading, caching
- **SEO**: Meta tags, structured data, SSR/SSG
- **Progressive Enhancement**: Works without JavaScript
- **Accessibility**: Keyboard navigation, screen readers

### Mobile Platform
- **iOS Considerations**: 
  - Swift/Objective-C interop if needed
  - App Store guidelines compliance
  - iOS-specific UI patterns
  - TestFlight for beta testing
- **Android Considerations**:
  - Kotlin/Java interop if needed
  - Google Play guidelines compliance
  - Android-specific UI patterns
  - Material Design adherence
- **Performance**: Battery usage, memory management
- **Offline-First**: Local data persistence
- **App Store Optimization**: Icons, screenshots, descriptions

### API Platform
- **Versioning**: API version strategy (v1, v2)
- **Documentation**: Always up-to-date
- **Rate Limiting**: Protect against abuse
- **Authentication**: OAuth2, JWT, API keys
- **Monitoring**: Request tracking, error rates
- **Caching**: Response caching strategies
- **Pagination**: Consistent pagination across endpoints

## Coordination Strategy

### Shared Repository Structure
```
project-root/
├── api/                 # Backend API
│   ├── src/
│   ├── tests/
│   └── docs/
├── web/                 # Web frontend
│   ├── src/
│   ├── tests/
│   └── public/
├── mobile/              # Mobile app
│   ├── src/
│   ├── ios/
│   ├── android/
│   └── tests/
├── shared/              # Shared types, models
│   ├── types/
│   └── constants/
└── docs/               # Documentation
    ├── api/
    ├── design/
    └── architecture/
```

### Shared Code
- **TypeScript Types**: Share between web and API
- **Validation Schemas**: Share validation logic
- **Constants**: Shared constants across platforms
- **Utilities**: Common utility functions
- **Design Tokens**: Shared colors, spacing, typography

### Communication
- **API Contract**: Single source of truth
- **Design System**: Shared Figma/Sketch files
- **Sprint Planning**: Coordinate feature work
- **Integration Points**: Clear handoff points
- **Blocking Issues**: Quickly resolve cross-platform blockers

## Example Multi-Platform Feature

**Comprehensive Prompt:**
"Build a 'user profile' feature across web, mobile, and API platforms:

**Shared API Contract:**
- GET /api/v1/users/:id - Get user profile
- PUT /api/v1/users/:id - Update user profile
- POST /api/v1/users/:id/avatar - Upload avatar

**Web Implementation:**
- React + TypeScript
- Profile view and edit modes
- Image upload with preview
- Form validation
- Responsive design

**Mobile Implementation:**
- React Native
- Same UI/UX as web
- Camera integration for avatar
- Offline support for profile viewing
- Push notification when profile updated

**API Documentation:**
- OpenAPI 3.0 spec
- Example requests in JS and Dart
- Error code reference
- Rate limiting details

Ensure consistency across all platforms and create integration tests."

## Best Practices

1. **API First**: Design API before implementations
2. **Design System**: Use shared design tokens
3. **Shared Types**: Share TypeScript/Flow types
4. **Consistent Errors**: Same error messages across platforms
5. **Coordinated Releases**: Release all platforms together
6. **Cross-Platform Testing**: Test integration points
7. **Documentation**: Keep docs synchronized
8. **Version Together**: Keep platform versions aligned

## Related Patterns

- `full-stack-feature.md` - Comprehensive feature development
- `api-scaffold.md` (tool) - Generate API boilerplate
- `mobile-app-scaffold.md` (tool) - Generate mobile app structure
- `api-documenter.md` (tool) - Generate API documentation
- `component-library.md` (tool) - Build shared component library
- `design-system.md` (tool) - Create design system
- `integration-test.md` (tool) - Cross-platform integration tests
