# Security Fundamentals for Cloud (CC ZG504) — Mid-Semester Solutions
## Table of contents

- [Q1. The Shared Responsibility Model](#q1-the-shared-responsibility-model)  
- [Q2. Identity and Access Management (IAM)](#q2-identity-and-access-management-iam)  
- [Q3. Cloud Infrastructure Security](#q3-cloud-infrastructure-security)  
- [Q4. Microservices Security](#q4-microservices-security)  
- [Q5. Serverless Security](#q5-serverless-security)  
- [Practice Quiz (optional)](#practice-quiz-optional)  
- [Contributing / Usage](#contributing--usage)

---

## Q1. The Shared Responsibility Model

**Summary:** The Shared Responsibility Model defines which security responsibilities belong to the Cloud Service Provider (CSP) and which belong to the customer. It clarifies "security **of** the cloud" (CSP) vs "security **in** the cloud" (customer).

### Model in detail
- **Security "of" the Cloud (CSP):** physical datacenters, host hardware, physical network, hypervisor and virtualization infrastructure, foundational services.
- **Security "in" the Cloud (Customer):** customer data, identity & access management, application code, OS (where applicable), network configuration, encryption keys (unless managed), and user behavior/configuration.

### Responsibility differences by service model
- **IaaS (Infrastructure as a Service)**  
  Customer responsibilities are greatest: guest OS, middleware, applications, data, network config, and identity.
- **PaaS (Platform as a Service)**  
  CSP manages OS and runtime; customer focuses on application code, data, and app configuration.
- **SaaS (Software as a Service)**  
  CSP manages the entire stack; customer mainly manages data, user access, and client-side security.

### Customer responsibility examples

| Model | Example customer responsibilities |
|---|---|
| **IaaS** | Patch and harden the guest OS (Windows/Linux); manage firewall rules and IAM for VMs. |
| **PaaS** | Secure application settings, manage API keys/credentials, validate inputs. |
| **SaaS** | Configure user permissions, enforce strong password/MFA policies, manage data lifecycle. |

---

## Q2. Identity and Access Management (IAM)

### Authentication vs Authorization
- **Authentication (AuthN):** Verifies *who* the user is (passwords, MFA, tokens, biometric).
- **Authorization (AuthZ):** Determines *what* an authenticated user can do (roles, policies, ACLs).

### SAML and Single Sign-On (SSO)
- **SAML (Security Assertion Markup Language):** An XML protocol used for federated authentication. An Identity Provider (IdP) vouches for a user and issues assertions to Service Providers (SPs), enabling SSO across domains without reentering credentials.

### When MFA is essential
- Enforce **Multi-Factor Authentication (MFA)** for high-risk actions and accounts:
  - Cloud root/management accounts
  - Administrative console access
  - DevOps/CI systems that can modify production

MFA mitigates risks from stolen credentials by requiring a second factor (TOTP, hardware token, push approval).

---

## Q3. Cloud Infrastructure Security

### Why secure compute resources?
Compute resources run code and process data. If compromised, they enable data exfiltration, lateral movement across the environment, privilege escalation, and misuse (e.g., cryptomining or botnets).

### VM image hardening best practices
1. **Remove unnecessary services:** Uninstall or disable unused packages and daemons (e.g., FTP, Telnet).
2. **Apply latest security patches:** Keep OS and libraries updated; automate vulnerability scanning and patching.
3. **Disable default accounts & credentials:** Remove or rename default users and ensure no default passwords exist; enforce strong authentication and keys.

Additional tips: enable disk encryption, minimize installed packages, use configuration management to ensure drift is detected, and sign/verify images before deployment.

---

## Q4. Microservices Security

### Specific risks with microservices
1. **Increased attack surface:** Many services expose APIs/endpoints—each must be authenticated, authorized, and monitored.
2. **Insecure inter-service communication:** Unencrypted or unauthenticated internal traffic enables eavesdropping, replay, or spoofing. A breach in one service can spread.

### API Gateway as a control point
An **API Gateway** centralizes controls for microservices:
- **Centralized authentication & token validation:** Validate JWTs/OAuth tokens once at the gateway rather than per service.
- **Rate limiting & throttling:** Protect backend services from abusive traffic and DoS.
- **Routing, request validation, and logging:** Enforce schemas, sanitize inputs, and provide a focal point for observability.

Also use mTLS for service-to-service authentication and a service mesh for fine-grained telemetry and policy enforcement.

---

## Q5. Serverless Security

### Event injection risk
**Event Injection** happens when attacker-controlled data is placed in event sources (e.g., object uploads, queue messages). If serverless functions trust and execute or use that data without validation, this can lead to command injection, SQL/NoSQL injection, or unwanted downstream effects.

### Secure permissions & least privilege
1. **Function-level IAM roles:** Give each function a dedicated role with exactly the permissions it needs — no wildcards or shared "catch-all" roles.
2. **Scoped resource policies:** Restrict functions to the minimum set of resources and actions (e.g., `s3:GetObject` for `arn:aws:s3:::my-app-bucket/*` only).

Additional best practices: validate & sanitize inputs, limit execution time and memory, use environment variables for configuration (kept in a secret manager), and monitor for anomalous invocation patterns.
---
