# Requirements Document: VerifAI Fraud Detection System

## Introduction

VerifAI is a prototype-grade, agentic artificial intelligence system designed to autonomously detect and respond to fraudulent financial transactions in real time. The system integrates a machine learning–based fraud probability model with an agentic decision pipeline that follows a structured Perceive → Reason → Decide → Act → Learn workflow.

This is a proof-of-concept system with explicit limitations and constraints designed for demonstration and evaluation purposes, not production deployment.

## Glossary

- **Transaction_Processor**: Core component that ingests and evaluates financial transactions
- **Fraud_Model**: XGBoost-based machine learning model that estimates fraud probability
- **Decision_Agent**: Agentic AI component that makes autonomous decisions following the PRDAL workflow
- **Notification_Service**: Component responsible for sending email alerts to users
- **Dashboard_Service**: Component providing user and admin interfaces
- **Auth_Service**: JWT-based authentication and authorization component
- **System**: The complete VerifAI fraud detection system

## Functional Requirements

### FR-1: Transaction Processing

**User Story:** As a financial institution, I want to process transactions in real time, so that I can detect fraud as it occurs.

#### Acceptance Criteria

1. WHEN a transaction is submitted to the system, THE Transaction_Processor SHALL ingest it immediately
2. WHEN a transaction is ingested, THE Transaction_Processor SHALL validate the transaction format and required fields
3. WHEN transaction validation fails, THE Transaction_Processor SHALL reject the transaction with a descriptive error message
4. WHEN a valid transaction is received, THE Transaction_Processor SHALL store it in memory for processing
5. THE Transaction_Processor SHALL maintain transaction history in memory during system operation

### FR-2: Fraud Probability Assessment

**User Story:** As a fraud analyst, I want the system to estimate fraud probability for each transaction, so that I can understand the risk level.

#### Acceptance Criteria

1. WHEN a valid transaction is processed, THE Fraud_Model SHALL calculate a fraud probability score between 0.0 and 1.0
2. THE Fraud_Model SHALL use XGBoost algorithm for probability estimation
3. THE Fraud_Model SHALL use synthetic training data for model training
4. WHEN the model encounters missing features, THE Fraud_Model SHALL handle them gracefully using default values
5. THE Fraud_Model SHALL return probability scores within 100ms of receiving transaction data

### FR-3: Autonomous Decision Making

**User Story:** As a system operator, I want the system to make autonomous decisions about transactions, so that fraud can be prevented without manual intervention.

#### Acceptance Criteria

1. WHEN a fraud probability is calculated, THE Decision_Agent SHALL follow the Perceive → Reason → Decide → Act → Learn workflow
2. WHEN fraud probability is below 0.3, THE Decision_Agent SHALL decide APPROVE
3. WHEN fraud probability is between 0.3 and 0.7, THE Decision_Agent SHALL decide HOLD
4. WHEN fraud probability is above 0.7, THE Decision_Agent SHALL decide BLOCK
5. THE Decision_Agent SHALL use hardcoded thresholds for decision boundaries
6. WHEN a decision is made, THE Decision_Agent SHALL record the decision with timestamp and reasoning

### FR-4: User Notifications

**User Story:** As a transaction owner, I want to receive notifications about my transaction status, so that I can take appropriate action.

#### Acceptance Criteria

1. WHEN a transaction is BLOCKED, THE Notification_Service SHALL send an email alert to the transaction owner
2. WHEN a transaction is HELD, THE Notification_Service SHALL send an email notification to the transaction owner
3. WHEN a transaction is APPROVED, THE Notification_Service SHALL NOT send any notification
4. THE Notification_Service SHALL use email as the primary notification channel
5. THE Notification_Service SHALL include transaction ID, decision, and timestamp in notifications

### FR-5: User Dashboard

**User Story:** As a transaction owner, I want to view my transaction history and status, so that I can monitor my account activity.

#### Acceptance Criteria

1. WHEN a user accesses the dashboard, THE Dashboard_Service SHALL display their transaction history
2. WHEN displaying transactions, THE Dashboard_Service SHALL show transaction ID, amount, timestamp, and status
3. WHEN a user views transaction details, THE Dashboard_Service SHALL display fraud probability and decision reasoning
4. THE Dashboard_Service SHALL provide real-time updates of transaction status
5. THE Dashboard_Service SHALL allow users to filter transactions by status and date range

### FR-6: Admin Dashboard

**User Story:** As a system administrator, I want to monitor system performance and transaction patterns, so that I can ensure proper operation.

#### Acceptance Criteria

1. WHEN an admin accesses the admin dashboard, THE Dashboard_Service SHALL display system-wide transaction statistics
2. THE Dashboard_Service SHALL show total transactions processed, approval rates, and fraud detection rates
3. THE Dashboard_Service SHALL provide transaction volume metrics over time
4. THE Dashboard_Service SHALL display model performance metrics including accuracy and response times
5. THE Dashboard_Service SHALL allow admins to view detailed logs of decision reasoning

### FR-7: Authentication and Authorization

**User Story:** As a system user, I want secure access to the system, so that my transaction data remains protected.

#### Acceptance Criteria

1. WHEN a user attempts to log in, THE Auth_Service SHALL validate credentials against the user database
2. WHEN credentials are valid, THE Auth_Service SHALL issue a JWT token with appropriate claims
3. WHEN a JWT token is presented, THE Auth_Service SHALL validate token signature and expiration
4. THE Auth_Service SHALL distinguish between user and admin roles in JWT claims
5. WHEN accessing protected endpoints, THE System SHALL require valid JWT authentication

### FR-8: API Interface

**User Story:** As an integrating system, I want REST APIs to interact with the fraud detection system, so that I can submit transactions and retrieve results.

#### Acceptance Criteria

1. THE System SHALL provide REST endpoints for transaction submission
2. THE System SHALL provide REST endpoints for transaction status retrieval
3. THE System SHALL provide REST endpoints for user authentication
4. WHEN API requests are made, THE System SHALL return appropriate HTTP status codes
5. THE System SHALL use JSON format for all API request and response payloads

## Non-Functional Requirements

### NFR-1: Performance

**User Story:** As a system operator, I want the system to process transactions quickly, so that real-time fraud detection is achieved.

#### Acceptance Criteria

1. THE System SHALL process individual transactions within 200ms end-to-end
2. THE System SHALL support concurrent processing of up to 100 transactions per second
3. THE Fraud_Model SHALL return probability scores within 100ms
4. THE Dashboard_Service SHALL load user interfaces within 2 seconds
5. THE System SHALL maintain response times under load during demonstration periods

### NFR-2: Reliability

**User Story:** As a system operator, I want the system to operate reliably during demonstrations, so that it showcases the intended functionality.

#### Acceptance Criteria

1. THE System SHALL maintain 95% uptime during demonstration periods
2. WHEN components fail, THE System SHALL log errors appropriately
3. THE System SHALL handle invalid input gracefully without crashing
4. THE System SHALL recover from temporary failures automatically where possible
5. THE System SHALL maintain data consistency in memory storage

### NFR-3: Usability

**User Story:** As a system evaluator, I want the system to be easy to understand and navigate, so that I can assess its capabilities effectively.

#### Acceptance Criteria

1. THE Dashboard_Service SHALL provide intuitive navigation between user and admin views
2. THE System SHALL display clear error messages for invalid operations
3. THE Dashboard_Service SHALL use consistent visual design across all interfaces
4. THE System SHALL provide clear documentation of API endpoints
5. THE Dashboard_Service SHALL be responsive across desktop and tablet devices

### NFR-4: Security

**User Story:** As a system operator, I want basic security measures in place, so that the prototype demonstrates security awareness.

#### Acceptance Criteria

1. THE Auth_Service SHALL use JWT tokens for stateless authentication
2. THE System SHALL validate all input parameters to prevent injection attacks
3. THE System SHALL use HTTPS for all API communications
4. THE Auth_Service SHALL implement proper password hashing for stored credentials
5. THE System SHALL not log sensitive information like passwords or full transaction details

## Constraints & Assumptions

### Technical Constraints

1. **Synthetic Data Only**: The system uses synthetic training data and test transactions
2. **In-Memory Storage**: All transaction history is stored in memory, not persisted to disk
3. **Hardcoded Thresholds**: Decision thresholds (0.3, 0.7) are hardcoded, not configurable
4. **No Continuous Learning**: The ML model does not update or retrain based on new data
5. **Monolithic Architecture**: Single FastAPI backend application, not microservices
6. **Email Notifications Only**: Only email notifications are implemented and active

### Operational Constraints

1. **Prototype Maturity**: System is designed for demonstration, not production use
2. **Limited Scalability**: System designed for demonstration load, not production scale
3. **No High Availability**: Single instance deployment, no redundancy or failover
4. **Development Environment**: Designed to run in development/demo environments only

### Data Constraints

1. **No Real Financial Data**: System processes only synthetic transaction data
2. **No External Integrations**: No connections to real payment gateways or financial institutions
3. **Limited Transaction Types**: Supports basic transaction structure only
4. **No Historical Data**: No pre-existing transaction history on system startup

## Explicit Exclusions

The following capabilities are explicitly NOT included in this system:

### Integration Exclusions
- **Live Payment Gateway Integration**: No connections to real payment processors
- **WhatsApp Notifications**: Configured but inactive notification channel
- **External Database Systems**: No persistent storage beyond in-memory

### Production Exclusions
- **CI/CD Pipelines**: No automated deployment or continuous integration
- **Regulatory Compliance**: No guarantees of financial regulation compliance
- **Encryption at Rest**: Data is not encrypted when stored in memory
- **Production Monitoring**: No production-grade logging, metrics, or alerting
- **Backup and Recovery**: No data backup or disaster recovery capabilities

### Scalability Exclusions
- **Horizontal Scaling**: No support for multiple instance deployment
- **Load Balancing**: No load distribution across multiple servers
- **Database Clustering**: No distributed data storage
- **Caching Layers**: No Redis or similar caching infrastructure

### Security Exclusions
- **Advanced Threat Protection**: No protection against sophisticated attacks
- **Audit Logging**: No comprehensive audit trail for compliance
- **Data Loss Prevention**: No DLP or data governance controls
- **Multi-Factor Authentication**: Only basic username/password authentication

## Future Requirements (Out of Scope)

The following requirements are identified for future development but are explicitly out of scope for this prototype:

### Enhanced ML Capabilities
- **Continuous Learning**: Model retraining based on feedback and new data
- **Multiple Model Support**: A/B testing of different fraud detection algorithms
- **Feature Engineering Pipeline**: Automated feature extraction and selection
- **Model Explainability**: Detailed explanations of model decisions

### Production Readiness
- **Persistent Storage**: Database integration for transaction history
- **Microservices Architecture**: Decomposition into independent services
- **Container Orchestration**: Kubernetes deployment and management
- **Production Monitoring**: Comprehensive observability and alerting

### Advanced Security
- **End-to-End Encryption**: Encryption of data in transit and at rest
- **OAuth2/OIDC Integration**: Enterprise identity provider integration
- **Role-Based Access Control**: Granular permission management
- **Security Scanning**: Automated vulnerability assessment

### Regulatory Compliance
- **PCI DSS Compliance**: Payment card industry security standards
- **GDPR Compliance**: Data protection and privacy regulations
- **SOX Compliance**: Financial reporting and audit requirements
- **AML Integration**: Anti-money laundering rule integration