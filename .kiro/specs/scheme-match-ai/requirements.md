# Requirements Document: SchemeMatch AI

## Introduction

SchemeMatch AI is an AI-powered government scheme discovery and application assistant designed to help Indian citizens identify welfare schemes they qualify for and generate ready-to-submit application packages. The system addresses the critical problem of 300M+ Indians being unaware of welfare schemes they qualify for, leading to ₹2.5 lakh crore in unutilized benefits annually.

The system uses a conversational AI interface to assess user eligibility through adaptive questioning, matches users with relevant schemes using RAG-based semantic search, extracts information from documents using OCR, and generates complete application packages. A key innovation is the use of AWS Kiro to automatically generate and maintain eligibility checking code from scheme policy documents.

## Glossary

- **System**: The SchemeMatch AI application
- **User**: Indian citizen seeking government welfare schemes
- **Scheme**: Government welfare program (central or state-level)
- **RAG**: Retrieval-Augmented Generation for semantic search
- **OCR**: Optical Character Recognition for document text extraction
- **Kiro**: AWS service for AI-powered code generation
- **Eligibility_Checker**: Python function that determines if a user qualifies for a scheme
- **Application_Package**: PDF bundle containing pre-filled forms, documents, and submission guide
- **CSC**: Common Service Center (government-authorized service point)
- **Confidence_Score**: Numerical value (0-1) indicating match certainty
- **Document_Type**: Category of identity/proof document (Aadhaar, PAN, Income Certificate, etc.)
- **Vector_DB**: Pinecone vector database for semantic search
- **Assessment**: Multi-turn conversational eligibility questionnaire

## Requirements

### Requirement 1: Conversational Eligibility Assessment

**User Story:** As a user, I want to answer questions about my situation in a conversational manner, so that the system can understand my eligibility for schemes without requiring technical knowledge.

#### Acceptance Criteria

1. WHEN a user starts an assessment, THE System SHALL initiate a multi-turn dialogue with 10-15 adaptive questions
2. WHEN a user provides an answer, THE System SHALL determine the next question based on previous responses
3. WHERE multilingual support is enabled, THE System SHALL support conversations in Hindi, Tamil, Telugu, Kannada, and Marathi
4. WHEN a user provides an invalid or unclear response, THE System SHALL request clarification with examples
5. WHEN the assessment is complete, THE System SHALL have collected sufficient data to evaluate eligibility across all scheme categories
6. THE System SHALL complete the assessment within 5 minutes for 80% of users
7. WHEN a user exits mid-assessment, THE System SHALL save progress and allow resumption within 24 hours

### Requirement 2: Scheme Matching Engine

**User Story:** As a user, I want to discover relevant government schemes based on my profile, so that I can access benefits I qualify for.

#### Acceptance Criteria

1. WHEN user assessment data is complete, THE System SHALL perform semantic search across the Vector_DB containing 50+ schemes
2. THE System SHALL return ranked scheme matches with Confidence_Scores between 0 and 1
3. WHEN displaying matches, THE System SHALL show the top 10 most relevant schemes
4. THE System SHALL include 10 central government schemes and 40 state-level schemes in the database
5. WHEN a scheme match is presented, THE System SHALL provide an explanation of why the user qualifies
6. THE System SHALL achieve 85% or higher relevance accuracy for top-3 results
7. THE System SHALL return search results within 3 seconds
8. WHEN multiple schemes have similar scores, THE System SHALL prioritize schemes with higher benefit amounts

### Requirement 3: Document Intelligence

**User Story:** As a user, I want to upload my documents and have the system automatically extract relevant information, so that I don't have to manually type all my details.

#### Acceptance Criteria

1. WHEN a user uploads a document, THE System SHALL classify it into one of 15 supported Document_Types
2. THE System SHALL support Aadhaar, PAN, Income Certificate, Caste Certificate, Ration Card, Bank Passbook, Voter ID, Driving License, Passport, Birth Certificate, Disability Certificate, Age Proof, Address Proof, Educational Certificate, and Land Records
3. WHEN a document is classified, THE System SHALL extract relevant fields using OCR
4. THE System SHALL achieve 80% or higher OCR accuracy on clear scans
5. WHEN a document has poor quality, THE System SHALL detect blur, low resolution, or expiry issues
6. IF a document quality check fails, THEN THE System SHALL request the user to re-upload a clearer image
7. WHEN a required document is missing, THE System SHALL suggest alternative acceptable documents
8. THE System SHALL process up to 1000 document pages per month within AWS Free Tier limits
9. WHEN extracting Aadhaar data, THE System SHALL mask the full Aadhaar number and display only last 4 digits

### Requirement 4: Kiro-Powered Code Generation

**User Story:** As a system administrator, I want eligibility checking code to be automatically generated from scheme policy documents, so that the system stays accurate without manual coding effort.

#### Acceptance Criteria

1. WHEN a new scheme PDF is provided, THE System SHALL use Kiro to analyze the document and generate an Eligibility_Checker function
2. THE Eligibility_Checker SHALL accept user_data as a dictionary parameter and return an eligibility result dictionary
3. THE System SHALL generate Python code with type hints, docstrings, and error handling
4. WHEN Kiro generates code, THE System SHALL also generate 5-10 unit tests covering edge cases
5. THE System SHALL generate document extraction functions for each supported Document_Type
6. WHEN a scheme policy changes, THE System SHALL detect the change and trigger Kiro to regenerate affected functions
7. THE System SHALL maintain version control of all generated code with timestamps and change logs
8. THE System SHALL validate generated code syntax before deployment
9. WHEN generated code fails validation, THE System SHALL log the error and alert administrators

### Requirement 5: Application Package Generation

**User Story:** As a user, I want to receive a complete application package with all forms and documents organized, so that I can easily submit my application to the relevant office.

#### Acceptance Criteria

1. WHEN a user selects a scheme to apply for, THE System SHALL generate an Application_Package as a PDF
2. THE Application_Package SHALL include a cover page with scheme name, user details, and application date
3. THE Application_Package SHALL include pre-filled application forms with user data from assessment and OCR
4. THE Application_Package SHALL include a document checklist showing which documents are attached and which are missing
5. THE Application_Package SHALL include a submission guide with office address, contact details, and step-by-step instructions
6. THE Application_Package SHALL include QR codes linking to official scheme websites
7. THE System SHALL NOT auto-submit applications to government portals
8. WHEN generating forms, THE System SHALL leave signature fields blank for manual signing
9. THE System SHALL allow users to download the Application_Package as a PDF file

### Requirement 6: Eligibility Decision Explainability

**User Story:** As a user, I want to understand why I qualify or don't qualify for a scheme, so that I can trust the system's recommendations and know what to improve.

#### Acceptance Criteria

1. WHEN displaying a scheme match, THE System SHALL show the reasoning behind the eligibility decision
2. THE System SHALL display Confidence_Scores as percentages with visual indicators
3. WHEN a user qualifies for a scheme, THE System SHALL list which criteria were met
4. WHEN a user does not qualify, THE System SHALL explain which criteria were not met and by how much
5. THE System SHALL provide links to official policy source documents
6. THE System SHALL achieve 90% or higher correctness in eligibility decisions
7. WHEN criteria are borderline, THE System SHALL indicate uncertainty and recommend manual verification

### Requirement 7: Data Privacy and Compliance

**User Story:** As a user, I want my personal data to be handled securely and deleted after use, so that my privacy is protected.

#### Acceptance Criteria

1. THE System SHALL store user data for a maximum of 30 days
2. WHEN 30 days have elapsed, THE System SHALL automatically delete all user personal data
3. THE System SHALL NOT perform Aadhaar authentication or verification
4. WHEN displaying the application, THE System SHALL show a disclaimer stating "Informational tool, not official government platform"
5. THE System SHALL NOT use government logos without proper permission
6. THE System SHALL encrypt all user data at rest using AES-256
7. THE System SHALL encrypt all data in transit using TLS 1.2 or higher
8. WHEN storing documents in S3, THE System SHALL use server-side encryption

### Requirement 8: System Performance and Scalability

**User Story:** As a system administrator, I want the system to handle multiple concurrent users efficiently within AWS Free Tier limits, so that we can serve users cost-effectively.

#### Acceptance Criteria

1. THE System SHALL support up to 100 concurrent users
2. THE System SHALL process eligibility assessments within 3 seconds per query
3. THE System SHALL stay within AWS Free Tier limits for Textract (1000 pages/month)
4. THE System SHALL stay within Pinecone free tier limits (100K vectors)
5. WHEN approaching usage limits, THE System SHALL alert administrators
6. IF usage limits are exceeded, THEN THE System SHALL queue requests and notify users of delays
7. THE System SHALL maintain 99% uptime during business hours (9 AM - 6 PM IST)

### Requirement 9: Multilingual Support

**User Story:** As a non-English speaking user, I want to interact with the system in my native language, so that I can understand and use the service effectively.

#### Acceptance Criteria

1. THE System SHALL support user interfaces in English, Hindi, Tamil, Telugu, Kannada, and Marathi
2. WHEN a user selects a language, THE System SHALL display all UI elements in that language
3. THE System SHALL translate scheme descriptions and eligibility criteria into the selected language
4. WHEN generating Application_Packages, THE System SHALL include bilingual content (English + selected language)
5. THE System SHALL use AWS Bedrock for translation services
6. THE System SHALL maintain translation accuracy of 85% or higher for domain-specific terms

### Requirement 10: Scheme Database Management

**User Story:** As a system administrator, I want to easily add, update, and monitor schemes in the database, so that the system stays current with policy changes.

#### Acceptance Criteria

1. THE System SHALL maintain a database of at least 50 schemes (10 central, 40 state-level)
2. WHEN a new scheme is added, THE System SHALL scrape the official policy document and create vector embeddings
3. THE System SHALL monitor scheme portals weekly for policy updates
4. WHEN a policy change is detected, THE System SHALL flag the scheme for review
5. THE System SHALL store scheme metadata including name, category, benefit amount, eligibility criteria, and source URL
6. THE System SHALL allow administrators to manually trigger scheme updates
7. THE System SHALL log all scheme changes with timestamps and version numbers

### Requirement 11: User Interface and Experience

**User Story:** As a user with limited technical literacy, I want a simple and intuitive interface, so that I can use the system without assistance.

#### Acceptance Criteria

1. THE System SHALL use Streamlit for the web interface
2. THE System SHALL display progress indicators during assessment, document processing, and package generation
3. WHEN a user makes an error, THE System SHALL display clear error messages with suggested corrections
4. THE System SHALL use large fonts and high-contrast colors for accessibility
5. THE System SHALL support voice input for users with low literacy
6. THE System SHALL provide tooltips and help text for all form fields
7. WHEN a user completes an action, THE System SHALL provide clear confirmation messages
8. THE System SHALL allow users to navigate back to previous steps without losing data

### Requirement 12: Testing and Quality Assurance

**User Story:** As a developer, I want comprehensive automated tests for all system components, so that we can maintain code quality and catch bugs early.

#### Acceptance Criteria

1. THE System SHALL include unit tests for all Eligibility_Checker functions
2. THE System SHALL include integration tests for the RAG pipeline
3. THE System SHALL include end-to-end tests for the complete user journey
4. THE System SHALL achieve 80% or higher code coverage
5. WHEN Kiro generates code, THE System SHALL automatically run generated unit tests
6. IF generated tests fail, THEN THE System SHALL prevent deployment and alert developers
7. THE System SHALL include performance tests to validate response time requirements
