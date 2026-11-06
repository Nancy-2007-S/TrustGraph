# TRUSTGRAPH
## Product Foundation & Engineering Blueprint
**Version:** 1.0  
---

# 1. Product Vision

TrustGraph is an explainable digital payment fraud detection and cross-institution risk intelligence platform.

It aims to identify suspicious transaction behaviour, detect potentially connected mule-account networks, explain why a transaction is considered risky, and demonstrate privacy-conscious sharing of risk signals between simulated financial institutions.

### Vision

Make suspicious digital payment patterns easier to identify, investigate and understand through explainable risk intelligence.

### Problem statement

Digital payment fraud can involve multiple accounts, institutions and successive transactions. When institutions examine their own transaction records independently, connected suspicious patterns may be difficult to identify.

TrustGraph will demonstrate how transaction-level risk analysis, graph intelligence and privacy-conscious information exchange can help identify these patterns.

### Proposed solution

A web-based platform with five main capabilities:

1. Transaction monitoring.
2. Hybrid rule-based and machine-learning risk assessment.
3. Connected-account and mule-network analysis.
4. Cross-institution risk signal exchange.
5. Explainable fraud investigation dashboard.

---

# 2. Target Users

| User | Problem | TrustGraph solution |
|---|---|---|
| Fraud analyst | Difficult to investigate suspicious transactions | Risk dashboard and investigation cases |
| Fintech developer | Needs transaction risk assessment | REST API |
| Institution administrator | Needs visibility into institutional risk | Institution dashboard |
| Compliance analyst | Needs traceable investigation decisions | Audit logs and case history |

The first version will use synthetic data and simulated institutions.

It will not connect to actual bank accounts or UPI infrastructure.

---

# 3. Product Scope

## MVP — Must have

- User authentication.
- Role-based access.
- Institution management.
- Synthetic transaction generation.
- Transaction ingestion API.
- Rule-based risk scoring.
- Risk explanation.
- Suspicious transaction alerts.
- Basic account relationship graph.
- Investigation status management.

## Version 2 — Advanced intelligence

- Python ML inference service.
- Supervised fraud classification.
- Anomaly detection.
- Graph-based mule-account identification.
- Cross-institution risk signals.
- Advanced investigation dashboard.
- Model evaluation reports.

## Version 3 — Production-oriented improvements

- Redis caching.
- Asynchronous transaction processing.
- Docker deployment.
- CI/CD.
- Monitoring and logging.
- Performance testing.
- Advanced privacy-preserving techniques.
- Institutional API integration.

### Explicitly out of scope initially

- Real UPI payment processing.
- Real bank integrations.
- Automatic blocking of real payments.
- Production fraud guarantees.
- Federated learning implementation.
- Complex microservices architecture.

We will add complexity only when we understand why it is needed.

---

# 4. Functional Requirements

| ID | Requirement |
|---|---|
| FR01 | Users can log in securely. |
| FR02 | Administrators can manage simulated institutions. |
| FR03 | Transactions can be created and ingested. |
| FR04 | The system validates transaction details. |
| FR05 | Transactions are evaluated by the risk engine. |
| FR06 | Risk assessments are persisted. |
| FR07 | Analysts can view suspicious transactions. |
| FR08 | Analysts can investigate and update cases. |
| FR09 | The system identifies connected suspicious accounts. |
| FR10 | Simulated institutions can exchange risk indicators. |
| FR11 | Sensitive actions are recorded in audit logs. |

# 5. Non-functional Requirements

- Security: Protect accounts, APIs and institution-specific data.
- Reliability: Handle invalid input and service failures gracefully.
- Maintainability: Separate application responsibilities into modules.
- Performance: Measure API latency and processing time.
- Scalability: Keep services modular so they can evolve.
- Usability: Build an accessible, responsive dashboard.
- Explainability: Store the reason behind every risk decision.
- Testability: Make core business logic independently testable.

---

# 6. Technology Decisions

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React | User interface |
| Language | TypeScript | Type safety |
| Frontend tooling | Vite | Development and build |
| Styling | Tailwind CSS | Responsive UI |
| Routing | React Router | Page navigation |
| Backend | Node.js + Express | REST API |
| Database | MongoDB | Persistent data |
| ODM | Mongoose | Database schemas |
| Authentication | JWT + password hashing | Secure authentication |
| ML service | Python + FastAPI | Model inference |
| ML libraries | Pandas, Scikit-learn | Data processing and ML |
| Testing | Vitest, Supertest, Pytest | Automated testing |
| API testing | Postman | Endpoint verification |
| Version control | Git + GitHub | Source control |
| Containers | Docker | Reproducible deployment |
| Cloud | AWS | Deployment and infrastructure |

Technology selection principle:

Every technology must have a clear purpose and must be understood before implementation.

---

# 7. System Architecture

```text
                  USER
                   |
                   v
          REACT + TYPESCRIPT
                   |
                   v
             EXPRESS API
                   |
          AUTHENTICATION
          VALIDATION
          AUTHORIZATION
                   |
                   v
          BUSINESS SERVICES
                   |
        +----------+-----------+
        |          |           |
        v          v           v
   TRANSACTION   RISK       INVESTIGATION
    SERVICE     ENGINE       SERVICE
        |          |
        |      +---+------+
        |      |          |
        |      v          v
        |   RULES       PYTHON ML
        |   ENGINE      SERVICE
        |      |          |
        |      +-----+----+
        |            |
        |            v
        |      RISK DECISION
        |            |
        +------------+
                     |
                     v
                  MONGODB
                     |
              +------+------+
              |             |
              v             v
         GRAPH ANALYSIS   AUDIT LOGS
```

### Architectural principle

Start with a modular monolith for the main application.

Keep the ML service separate because Python model training and inference have a different runtime.

Do not introduce distributed infrastructure until the requirements justify it.

---

# 8. Database Design

Initial MongoDB collections:

### users
- name
- email
- passwordHash
- role
- institutionId
- createdAt

### institutions
- name
- institutionCode
- status
- createdAt

### accounts
- institutionId
- syntheticAccountId
- accountType
- createdAt

### transactions
- transactionId
- senderAccountId
- receiverAccountId
- institutionId
- amount
- timestamp
- status

### risk_assessments
- transactionId
- riskScore
- riskLevel
- contributingFactors
- modelVersion
- evaluatedAt

### alerts
- transactionId
- riskAssessmentId
- status
- assignedAnalyst
- createdAt

### investigations
- alertId
- caseStatus
- notes
- resolution
- updatedAt

### intelligence_signals
- pseudonymousAccountReference
- originatingInstitution
- riskIndicators
- createdAt
- expiresAt

### audit_logs
- actorId
- action
- resourceType
- resourceId
- timestamp

Database decisions, indexes, references and validation rules will be documented as we implement them.

---

# 9. Initial API Blueprint

| Method | Endpoint | Responsibility |
|---|---|---|
| POST | /api/auth/register | Register user |
| POST | /api/auth/login | Authenticate |
| GET | /api/institutions | List permitted institutions |
| POST | /api/transactions | Create transaction |
| GET | /api/transactions | Retrieve permitted transactions |
| GET | /api/transactions/:id | Transaction details |
| POST | /api/risk/evaluate/:id | Evaluate risk |
| GET | /api/alerts | Retrieve alerts |
| PATCH | /api/alerts/:id | Update alert |
| GET | /api/accounts/:id/network | Retrieve account network |
| POST | /api/intelligence/signals | Submit a risk signal |
| GET | /api/dashboard/summary | Dashboard statistics |

All routes will be versioned and documented. Access controls will be enforced on the server, not just in the frontend.

---

# 10. Risk Assessment Design

The first implementation will use a transparent rule-based engine.

Example indicators:

- Unusually large transaction.
- Newly added receiver.
- High transaction frequency.
- Unusual transaction timing.
- Connection to a previously suspicious account.

The engine will produce:

```json
{
  "transactionId": "TX1001",
  "riskScore": 75,
  "riskLevel": "HIGH",
  "reasons": [
    "UNUSUAL_AMOUNT",
    "NEW_RECEIVER",
    "HIGH_TRANSACTION_FREQUENCY"
  ]
}
```

These are illustrative fields and scores.

Later, machine-learning predictions will be incorporated as a separate source of evidence.

The final risk assessment must remain explainable and must not treat a model prediction as proof of fraud.

---

# 11. Development Principles

1. No blind copy-pasting.
2. Understand the concept before implementation.
3. Write pseudocode before complex functions.
4. Build small features independently.
5. Test every API.
6. Use Git commits for meaningful changes.
7. Maintain documentation.
8. Investigate errors instead of randomly changing code.
9. Never expose passwords, tokens or secrets.
10. Never claim an untested feature is complete.

### Definition of Done

A feature is complete only when:

- Its purpose is understood.
- Its implementation is understood.
- Valid inputs work.
- Invalid inputs are handled.
- Relevant tests pass.
- Errors are handled.
- Documentation is updated.
- Its operation can be explained without copying notes.

---

# 12. Project Success Criteria

By Day 90, the target is:

- A complete deployed application.
- Working frontend and backend integration.
- Persistent MongoDB storage.
- Secure authentication and authorization.
- Explainable transaction risk assessment.
- An evaluated ML model.
- Graph-based account analysis.
- Simulated institutional risk intelligence.
- Automated tests.
- CI/CD pipeline.
- Architecture and API documentation.
- A working product demonstration.
- A technical presentation suitable for hackathons and placements.

Actual performance measurements will be added only after testing.

---

# 13. Long-term Direction

TrustGraph begins as a student-built research prototype.

Its longer-term direction is an explainable fraud intelligence platform for fintechs and financial institutions.

Potential expansion:

- Risk assessment APIs.
- Institution-level intelligence.
- Advanced graph analytics.
- Privacy-enhancing computation.
- Real-world integrations subject to regulatory and institutional authorization.
- Enterprise analytics and investigation workflows.

**Our engineering objective:** Build a product that is understandable, measurable, testable and extensible — not merely visually impressive.