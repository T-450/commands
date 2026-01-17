# Security Hardening Workflow - GitHub Copilot Instructions

## Overview

This workflow implements security-first architecture by prioritizing security at every layer of the application stack. It coordinates security assessment, architecture review, implementation, compliance verification, and monitoring to create a comprehensive security posture.

## When to Use

**Prompt GitHub Copilot with:**
- "Apply security hardening workflow to the authentication system"
- "Implement security-first approach for payment processing feature"
- "Harden security across entire application stack using security-hardening workflow"
- "Conduct comprehensive security audit and implement fixes"

## Workflow Phases

### Phase 1: Security Assessment

#### Step 1: Initial Security Audit

**Prompt GitHub Copilot:**
"Perform comprehensive security audit on [application/feature/module]. Identify vulnerabilities across all components including OWASP Top 10, compliance gaps (GDPR/SOC2/PCI-DSS), and security risks in infrastructure, code, dependencies, and configurations."

Security audit scope:

**Application Security**:
- OWASP Top 10 vulnerabilities
- Authentication and authorization flaws
- Input validation gaps
- Injection vulnerabilities (SQL, XSS, Command)
- Insecure deserialization
- Security misconfiguration
- Sensitive data exposure
- Insufficient logging and monitoring

**Infrastructure Security**:
- Network security (firewalls, segmentation)
- Access controls and IAM
- Secrets management
- Encryption in transit and at rest
- Container security
- Cloud security posture
- API gateway security

**Dependencies**:
- Known vulnerabilities (CVEs)
- Outdated packages
- Malicious packages
- License compliance

**Compliance**:
- GDPR compliance gaps
- SOC2 requirements
- PCI-DSS (if handling payments)
- HIPAA (if handling health data)
- Industry-specific regulations

Deliverables:
- Vulnerability report with severity ratings
- Risk assessment matrix
- Compliance gap analysis
- Prioritized remediation roadmap

#### Step 2: Architecture Security Review

**Prompt GitHub Copilot:**
"Review and redesign architecture for security: [application/feature]. Focus on secure service boundaries, data isolation, defense in depth, zero-trust principles, and secure communication. Use findings from security audit to inform design."

Secure architecture principles:

**Defense in Depth**:
- Multiple security layers
- Fail-secure defaults
- Least privilege access
- Secure by design

**Service Boundaries**:
- Network segmentation
- Service isolation
- API gateway patterns
- Microservices security

**Data Security**:
- Data classification (public, internal, confidential, restricted)
- Encryption at rest
- Encryption in transit
- Data masking and tokenization
- Secure data deletion

**Zero Trust**:
- Never trust, always verify
- Micro-segmentation
- Continuous authentication
- Least privilege access

Deliverables:
- Secure architecture diagram
- Service isolation strategy
- Data flow diagrams with security controls
- Threat model documentation

### Phase 2: Security Implementation

#### Step 3: Backend Security Hardening

**Prompt GitHub Copilot:**
"Implement backend security measures for [application/feature]. Include robust authentication (OAuth2/JWT), fine-grained authorization (RBAC/ABAC), comprehensive input validation, SQL injection prevention, secure data handling, rate limiting, and security headers. Address findings from security audit."

Backend security implementations:

**Authentication**:
```python
# Example: Secure JWT implementation
import jwt
from datetime import datetime, timedelta
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

class AuthService:
    def __init__(self, secret_key: str, algorithm: str = "HS256"):
        self.secret_key = secret_key
        self.algorithm = algorithm
    
    def hash_password(self, password: str) -> str:
        return pwd_context.hash(password)
    
    def verify_password(self, plain: str, hashed: str) -> bool:
        return pwd_context.verify(plain, hashed)
    
    def create_access_token(self, data: dict, expires_delta: timedelta = None):
        to_encode = data.copy()
        expire = datetime.utcnow() + (expires_delta or timedelta(minutes=15))
        to_encode.update({"exp": expire, "iat": datetime.utcnow()})
        return jwt.encode(to_encode, self.secret_key, algorithm=self.algorithm)
```

**Authorization**:
```python
# Role-Based Access Control (RBAC)
from enum import Enum
from functools import wraps

class Role(Enum):
    ADMIN = "admin"
    USER = "user"
    GUEST = "guest"

def require_role(required_role: Role):
    def decorator(func):
        @wraps(func)
        async def wrapper(request, *args, **kwargs):
            user_role = request.state.user.role
            if user_role not in get_role_hierarchy(required_role):
                raise PermissionDenied("Insufficient permissions")
            return await func(request, *args, **kwargs)
        return wrapper
    return decorator

@router.post("/admin/users")
@require_role(Role.ADMIN)
async def create_user(user_data: UserCreate):
    # Only admins can create users
    pass
```

**Input Validation**:
```python
# Comprehensive input validation
from pydantic import BaseModel, validator, EmailStr
import re

class UserInput(BaseModel):
    email: EmailStr
    password: str
    name: str
    
    @validator('password')
    def password_strength(cls, v):
        if len(v) < 12:
            raise ValueError('Password must be at least 12 characters')
        if not re.search(r'[A-Z]', v):
            raise ValueError('Password must contain uppercase letter')
        if not re.search(r'[a-z]', v):
            raise ValueError('Password must contain lowercase letter')
        if not re.search(r'[0-9]', v):
            raise ValueError('Password must contain number')
        if not re.search(r'[^A-Za-z0-9]', v):
            raise ValueError('Password must contain special character')
        return v
    
    @validator('name')
    def sanitize_name(cls, v):
        # Remove any HTML/script tags
        return re.sub(r'<[^>]*>', '', v).strip()
```

**SQL Injection Prevention**:
```python
# Always use parameterized queries
async def get_user(user_id: int):
    # SECURE: Parameterized query
    query = "SELECT * FROM users WHERE id = $1"
    return await db.fetch_one(query, user_id)
    
    # INSECURE: Never do this!
    # query = f"SELECT * FROM users WHERE id = {user_id}"
```

**Security Headers**:
```python
# Security headers middleware
from starlette.middleware.base import BaseHTTPMiddleware

class SecurityHeadersMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request, call_next):
        response = await call_next(request)
        response.headers['X-Content-Type-Options'] = 'nosniff'
        response.headers['X-Frame-Options'] = 'DENY'
        response.headers['X-XSS-Protection'] = '1; mode=block'
        response.headers['Strict-Transport-Security'] = 'max-age=31536000; includeSubDomains'
        response.headers['Content-Security-Policy'] = "default-src 'self'"
        response.headers['Referrer-Policy'] = 'strict-origin-when-cross-origin'
        return response
```

#### Step 4: Infrastructure Security

**Prompt GitHub Copilot:**
"Implement infrastructure security for [application/feature]. Configure firewalls and network policies, implement secure secrets management (HashiCorp Vault/AWS Secrets Manager), apply least privilege IAM policies, set up security monitoring and logging, enable encryption everywhere, and implement intrusion detection."

Infrastructure security:

**Secrets Management**:
- Never commit secrets to code
- Use secret managers (Vault, AWS Secrets Manager, Azure Key Vault)
- Rotate secrets regularly
- Audit secret access
- Encrypt secrets at rest

**Network Security**:
- Network segmentation
- Security groups / firewall rules
- VPC configuration
- Private subnets for sensitive resources
- Bastion hosts for access
- VPN for remote access

**Encryption**:
- TLS 1.3 for all communications
- Encrypt data at rest (databases, storage)
- Certificate management
- Key rotation policies

**IAM and Access Control**:
```yaml
# Example: Least privilege IAM policy (AWS)
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/app-data/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:Query"
      ],
      "Resource": "arn:aws:dynamodb:region:account:table/MyTable"
    }
  ]
}
```

#### Step 5: Frontend Security

**Prompt GitHub Copilot:**
"Implement frontend security measures for [application/feature]. Include Content Security Policy headers, XSS prevention through output encoding, CSRF protection with tokens, secure authentication flows (OAuth2), secure session management, and sensitive data handling (no sensitive data in localStorage)."

Frontend security implementations:

**Content Security Policy**:
```html
<!-- Strict CSP -->
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; 
               script-src 'self' 'nonce-{random}'; 
               style-src 'self' 'nonce-{random}'; 
               img-src 'self' data: https:; 
               font-src 'self'; 
               connect-src 'self' https://api.example.com; 
               frame-ancestors 'none';">
```

**XSS Prevention**:
```javascript
// Use framework built-in escaping (React, Vue, Angular)
// React automatically escapes
function UserProfile({ user }) {
  return <div>{user.name}</div>; // Automatically escaped
}

// For dangerouslySetInnerHTML, sanitize first
import DOMPurify from 'dompurify';

function RichContent({ html }) {
  return (
    <div dangerouslySetInnerHTML={{ 
      __html: DOMPurify.sanitize(html) 
    }} />
  );
}
```

**CSRF Protection**:
```javascript
// Include CSRF token in requests
const csrfToken = document.querySelector('meta[name="csrf-token"]').content;

fetch('/api/endpoint', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'X-CSRF-Token': csrfToken
  },
  body: JSON.stringify(data)
});
```

**Secure Storage**:
```javascript
// Never store sensitive data in localStorage
// Use httpOnly, secure cookies for tokens
// Or use sessionStorage for less sensitive data

// GOOD: Store in httpOnly cookie (set by backend)
// GOOD: Store non-sensitive UI state
sessionStorage.setItem('theme', 'dark');

// BAD: Never do this
localStorage.setItem('authToken', token); // ❌
localStorage.setItem('password', password); // ❌
```

### Phase 3: Compliance and Testing

#### Step 6: Compliance Verification

**Prompt GitHub Copilot:**
"Verify compliance with security standards for [application/feature]. Check OWASP Top 10, GDPR privacy requirements, SOC2 controls, PCI-DSS (if applicable), industry-specific regulations. Validate all security implementations and provide compliance report with any remaining gaps."

Compliance frameworks:

**OWASP Top 10**:
- A01: Broken Access Control
- A02: Cryptographic Failures
- A03: Injection
- A04: Insecure Design
- A05: Security Misconfiguration
- A06: Vulnerable and Outdated Components
- A07: Identification and Authentication Failures
- A08: Software and Data Integrity Failures
- A09: Security Logging and Monitoring Failures
- A10: Server-Side Request Forgery

**GDPR**:
- Data protection by design
- Right to access
- Right to erasure
- Data portability
- Consent management
- Data breach notification
- Privacy by default

#### Step 7: Security Testing

**Prompt GitHub Copilot:**
"Create comprehensive security test suites for [application/feature]. Include penetration tests, security regression tests, automated vulnerability scanning (SAST/DAST), dependency scanning, and fuzzing. Integrate into CI/CD pipeline."

Security testing:

**Penetration Testing**:
- Authentication bypass attempts
- Authorization testing
- Injection testing (SQL, XSS, Command)
- Session management testing
- API security testing
- Infrastructure penetration testing

**Automated Security Tests**:
```python
# Example: Security regression tests
import pytest
from app import app, db

def test_sql_injection_prevention():
    """Test that SQL injection is prevented"""
    malicious_input = "1 OR 1=1; DROP TABLE users--"
    response = app.test_client().get(f'/user?id={malicious_input}')
    assert response.status_code == 400
    # Verify table still exists
    assert db.execute("SELECT 1 FROM users LIMIT 1")

def test_xss_prevention():
    """Test that XSS is prevented"""
    xss_payload = "<script>alert('XSS')</script>"
    response = app.test_client().post('/comment', json={'text': xss_payload})
    assert '<script>' not in response.get_data(as_text=True)
    assert '&lt;script&gt;' in response.get_data(as_text=True)

def test_authentication_required():
    """Test that endpoints require authentication"""
    response = app.test_client().get('/api/protected')
    assert response.status_code == 401

def test_authorization_enforced():
    """Test that authorization is enforced"""
    user_token = login_as_user()
    admin_endpoint = '/api/admin/users'
    response = app.test_client().get(
        admin_endpoint,
        headers={'Authorization': f'Bearer {user_token}'}
    )
    assert response.status_code == 403
```

### Phase 4: Deployment and Monitoring

#### Step 8: Secure Deployment

**Prompt GitHub Copilot:**
"Implement secure deployment pipeline for [application/feature]. Include security gates (vulnerability thresholds), container image scanning, secrets scanning, SAST/DAST in CI/CD, secure configuration management, and rollback procedures."

Secure CI/CD:
```yaml
# Security-focused CI/CD pipeline
name: Secure Deployment

on: [push, pull_request]

jobs:
  security-checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Secret scanning
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          
      - name: SAST scanning
        uses: github/super-linter@v4
        
      - name: Dependency scanning
        run: |
          npm audit --production --audit-level=high
          
      - name: Container scanning
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          severity: 'CRITICAL,HIGH'
          exit-code: '1'
          
      - name: DAST scanning
        run: |
          docker run -t owasp/zap2docker-stable zap-baseline.py \
            -t https://staging.example.com
```

#### Step 9: Security Monitoring Setup

**Prompt GitHub Copilot:**
"Set up security monitoring and incident response for [application/feature]. Include intrusion detection (IDS/IPS), log analysis with SIEM, automated alerting for security events, anomaly detection, and incident response procedures."

Security monitoring:

**Logging**:
- Authentication events (success/failure)
- Authorization failures
- Input validation failures
- Security exceptions
- Configuration changes
- Admin actions
- Data access patterns

**Alerting**:
- Failed login attempts (brute force)
- Privilege escalation attempts
- Unusual data access patterns
- Infrastructure changes
- Vulnerability scanner detections
- DDoS attacks

**SIEM Integration**:
- Centralized log aggregation
- Real-time analysis
- Threat intelligence integration
- Automated response playbooks

## Security Checklist

Before deployment:
- [ ] Security audit completed
- [ ] All critical vulnerabilities fixed
- [ ] Authentication properly implemented
- [ ] Authorization enforced on all endpoints
- [ ] Input validation comprehensive
- [ ] SQL injection prevented
- [ ] XSS prevention implemented
- [ ] CSRF protection enabled
- [ ] Security headers configured
- [ ] Secrets properly managed
- [ ] Encryption enabled (transit and rest)
- [ ] Security tests passing
- [ ] Compliance requirements met
- [ ] Security monitoring configured
- [ ] Incident response plan documented

## Related Patterns

- `full-review.md` - Security review component
- `feature-development.md` - Secure development practices
- `smart-fix.md` - Fix security vulnerabilities
- `security-scan.md` (tool) - Automated security scanning
- `compliance-check.md` (tool) - Compliance validation
- `penetration-test.md` (tool) - Security testing
- `incident-response.md` - Handle security incidents
