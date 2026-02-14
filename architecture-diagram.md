# FutureForge AI - Architecture Diagrams

## Document Information
- **Version:** 1.0
- **Date:** February 14, 2026
- **Project:** FutureForge AI - System Architecture
- **Target:** Amazon AI for Bharat Hackathon
- **Cloud Provider:** AWS (Asia Pacific - Mumbai Region)

---

## Table of Contents

1. [High-Level System Architecture](#1-high-level-system-architecture)
2. [AWS Cloud Infrastructure](#2-aws-cloud-infrastructure)
3. [AI/ML Pipeline Architecture](#3-aiml-pipeline-architecture)
4. [Resume Intelligence Pipeline](#4-resume-intelligence-pipeline)
5. [Burnout Detection & Recovery Flow](#5-burnout-detection--recovery-flow)
6. [Data Flow Diagrams](#6-data-flow-diagrams)
7. [Component Interaction Diagrams](#7-component-interaction-diagrams)
8. [Deployment Architecture](#8-deployment-architecture)

---

## 1. High-Level System Architecture

### 1.1 Three-Layer Architecture

```mermaid
graph TB
    subgraph "Client Layer"
        WEB[Web App - Next.js]
        MOBILE[Mobile App - React Native]
    end
    
    subgraph "API & Gateway Layer"
        APIGW[API Gateway]
        AUTH[Cognito Auth]
        WAF[AWS WAF]
    end
    
    subgraph "Business Logic Layer - Serverless"
        CAREER[Career Simulation Engine]
        RESUME[Resume Intelligence Engine]
        PLANNER[Adaptive Planner Engine]
        BURNOUT[Burnout Monitor Engine]
        PROGRESS[Progress Intelligence Engine]
    end
    
    subgraph "AI/ML Layer"
        BEDROCK[Amazon Bedrock]
        SAGEMAKER[SageMaker Models]
        COMPREHEND[Amazon Comprehend]
    end
    
    subgraph "Data Layer"
        DDB[DynamoDB]
        S3[S3 Storage]
        CACHE[ElastiCache Redis]
        SEARCH[OpenSearch]
    end
    
    subgraph "Integration Layer"
        EVENTS[EventBridge]
        STEPFN[Step Functions]
        SQS[SQS Queues]
    end
    
    WEB --> APIGW
    MOBILE --> APIGW
    APIGW --> WAF
    WAF --> AUTH
    AUTH --> CAREER
    AUTH --> RESUME
    AUTH --> PLANNER
    AUTH --> BURNOUT
    AUTH --> PROGRESS
    
    CAREER --> BEDROCK
    CAREER --> SAGEMAKER
    RESUME --> BEDROCK
    RESUME --> COMPREHEND
    PLANNER --> SAGEMAKER
    BURNOUT --> COMPREHEND
    BURNOUT --> SAGEMAKER
    
    CAREER --> DDB
    RESUME --> DDB
    PLANNER --> DDB
    BURNOUT --> DDB
    PROGRESS --> DDB
    
    RESUME --> S3
    CAREER --> CACHE
    RESUME --> SEARCH
    
    CAREER --> EVENTS
    RESUME --> EVENTS
    PLANNER --> EVENTS
    BURNOUT --> EVENTS
    
    EVENTS --> STEPFN
    EVENTS --> SQS
    
    style CAREER fill:#0066FF
    style RESUME fill:#0066FF
    style PLANNER fill:#0066FF
    style BURNOUT fill:#FF5252
    style PROGRESS fill:#00C853
```

**Requirements Mapping:**
- Client Layer: REQ-NFR-032 (Responsive design)
- API Gateway: REQ-NFR-003, REQ-NFR-021 (Rate limiting)
- Auth: REQ-NFR-019, REQ-NFR-020 (Authentication)
- Engines: REQ-SIM-006, REQ-RES-003, REQ-PLAN-001, REQ-BURN-016, REQ-PROG-001
- Data Layer: REQ-INT-010, REQ-NFR-017 (Encryption)

---

## 2. AWS Cloud Infrastructure

### 2.1 Complete AWS Architecture

```mermaid
graph TB
    subgraph "Edge & CDN"
        CF[CloudFront CDN]
        R53[Route53 DNS]
    end
    
    subgraph "API Gateway & Security"
        APIGW[API Gateway REST API]
        WAF[AWS WAF]
        COGNITO[Cognito User Pool]
    end
    
    subgraph "Compute - Lambda Functions"
        L1[Resume Upload Handler]
        L2[Resume Parser Lambda]
        L3[Resume Analysis Lambda]
        L4[Career Simulation Lambda]
        L5[Skill Gap Analyzer Lambda]
        L6[Study Planner Lambda]
        L7[Plan Rebalancer Lambda]
        L8[Burnout Monitor Lambda]
        L9[Mood Processor Lambda]
        L10[Progress Tracker Lambda]
    end
    
    subgraph "AI/ML Services"
        BEDROCK[Amazon Bedrock<br/>Claude 3 Sonnet/Haiku]
        SAGEMAKER[SageMaker Endpoints<br/>Custom ML Models]
        COMPREHEND[Amazon Comprehend<br/>Sentiment Analysis]
        TEXTRACT[Amazon Textract<br/>Document Parsing]
    end
    
    subgraph "Data Storage"
        DDB[DynamoDB<br/>Main Table]
        S3RESUME[S3 Bucket<br/>Resume Storage]
        S3DATA[S3 Bucket<br/>Market Data]
        OPENSEARCH[OpenSearch<br/>Job Market Index]
    end
    
    subgraph "Caching & Performance"
        REDIS[ElastiCache Redis<br/>Session & Data Cache]
    end
    
    subgraph "Event Processing"
        EVENTBRIDGE[EventBridge<br/>Event Bus]
        SQS[SQS Queue<br/>Async Processing]
        SNS[SNS Topics<br/>Notifications]
        STEPFN[Step Functions<br/>Workflows]
    end
    
    subgraph "Monitoring & Logging"
        CW[CloudWatch<br/>Logs & Metrics]
        XRAY[X-Ray<br/>Distributed Tracing]
    end
    
    R53 --> CF
    CF --> APIGW
    APIGW --> WAF
    WAF --> COGNITO
    
    COGNITO --> L1
    COGNITO --> L3
    COGNITO --> L4
    COGNITO --> L6
    COGNITO --> L8
    COGNITO --> L10
    
    L1 --> S3RESUME
    L1 --> TEXTRACT
    L1 --> EVENTBRIDGE
    
    EVENTBRIDGE --> L2
    L2 --> DDB
    L2 --> EVENTBRIDGE
    
    EVENTBRIDGE --> L3
    L3 --> BEDROCK
    L3 --> TEXTRACT
    L3 --> OPENSEARCH
    L3 --> DDB
    L3 --> EVENTBRIDGE
    
    EVENTBRIDGE --> L4
    L4 --> BEDROCK
    L4 --> SAGEMAKER
    L4 --> OPENSEARCH
    L4 --> DDB
    L4 --> REDIS
    L4 --> EVENTBRIDGE
    
    L4 --> L5
    L5 --> OPENSEARCH
    L5 --> DDB
    
    EVENTBRIDGE --> L6
    L6 --> BEDROCK
    L6 --> SAGEMAKER
    L6 --> DDB
    L6 --> EVENTBRIDGE
    
    EVENTBRIDGE --> L7
    L7 --> DDB
    L7 --> EVENTBRIDGE
    
    L8 --> L9
    L9 --> COMPREHEND
    L9 --> SAGEMAKER
    L9 --> DDB
    L9 --> EVENTBRIDGE
    
    EVENTBRIDGE --> L7
    
    L10 --> DDB
    
    EVENTBRIDGE --> SQS
    EVENTBRIDGE --> SNS
    EVENTBRIDGE --> STEPFN
    
    L1 --> CW
    L2 --> CW
    L3 --> CW
    L4 --> CW
    L6 --> CW
    L8 --> CW
    
    L3 --> XRAY
    L4 --> XRAY
    L6 --> XRAY
    
    style L1 fill:#FFA726
    style L2 fill:#FFA726
    style L3 fill:#0066FF
    style L4 fill:#0066FF
    style L5 fill:#0066FF
    style L6 fill:#00C853
    style L7 fill:#00C853
    style L8 fill:#FF5252
    style L9 fill:#FF5252
    style L10 fill:#9C27B0
```

**Requirements Mapping:**
- CloudFront: REQ-NFR-045, REQ-NFR-046 (Low bandwidth)
- API Gateway: REQ-NFR-003, REQ-NFR-021 (Performance, Rate limiting)
- Cognito: REQ-NFR-019, REQ-NFR-020 (Auth, Password policy)
- Lambda: REQ-NFR-001, REQ-NFR-002 (Performance)
- Bedrock: REQ-SIM-006, REQ-RES-003 (AI generation)
- DynamoDB: REQ-INT-010, REQ-NFR-013 (Data persistence, Backup)
- S3: REQ-SIM-001, REQ-NFR-017 (Resume storage, Encryption)
- EventBridge: REQ-INT-009, REQ-NFR-014 (Event flow, Reliability)

---

## 3. AI/ML Pipeline Architecture

### 3.1 AI Model Integration Flow

```mermaid
graph TB
    subgraph "Input Layer"
        USER[User Input]
        RESUME[Resume Data]
        PROFILE[User Profile]
    end
    
    subgraph "AI Orchestration"
        ROUTER[AI Model Router]
        CACHE[Response Cache]
    end
    
    subgraph "Amazon Bedrock Models"
        HAIKU[Claude 3 Haiku<br/>Fast, Cheap<br/>Resume Analysis]
        SONNET[Claude 3 Sonnet<br/>Balanced<br/>Career Simulation]
        OPUS[Claude 3 Opus<br/>Complex<br/>Advanced Reasoning]
    end
    
    subgraph "SageMaker Custom Models"
        BURNOUT_MODEL[Burnout Prediction<br/>XGBoost Model]
        VELOCITY_MODEL[Learning Velocity<br/>LSTM Model]
        PLACEMENT_MODEL[Placement Success<br/>Random Forest]
    end
    
    subgraph "AWS AI Services"
        COMPREHEND[Comprehend<br/>Sentiment Analysis]
        TEXTRACT[Textract<br/>Document Parsing]
    end
    
    subgraph "Post-Processing"
        VALIDATOR[Response Validator]
        FORMATTER[Output Formatter]
    end
    
    subgraph "Output Layer"
        TRAJECTORY[Career Trajectory]
        ANALYSIS[Resume Analysis]
        PLAN[Study Plan]
        RISK[Burnout Risk]
    end
    
    USER --> ROUTER
    RESUME --> ROUTER
    PROFILE --> ROUTER
    
    ROUTER --> CACHE
    CACHE -->|Cache Miss| HAIKU
    CACHE -->|Cache Miss| SONNET
    CACHE -->|Cache Miss| OPUS
    
    ROUTER --> BURNOUT_MODEL
    ROUTER --> VELOCITY_MODEL
    ROUTER --> PLACEMENT_MODEL
    
    ROUTER --> COMPREHEND
    ROUTER --> TEXTRACT
    
    HAIKU --> VALIDATOR
    SONNET --> VALIDATOR
    OPUS --> VALIDATOR
    BURNOUT_MODEL --> VALIDATOR
    VELOCITY_MODEL --> VALIDATOR
    PLACEMENT_MODEL --> VALIDATOR
    COMPREHEND --> VALIDATOR
    TEXTRACT --> VALIDATOR
    
    VALIDATOR --> FORMATTER
    
    FORMATTER --> TRAJECTORY
    FORMATTER --> ANALYSIS
    FORMATTER --> PLAN
    FORMATTER --> RISK
    
    FORMATTER -->|Cache Response| CACHE
    
    style HAIKU fill:#FFE082
    style SONNET fill:#FFA726
    style OPUS fill:#FF6F00
    style BURNOUT_MODEL fill:#FF5252
    style VELOCITY_MODEL fill:#9C27B0
    style PLACEMENT_MODEL fill:#00C853
```

**Requirements Mapping:**
- Model Router: REQ-NFR-015 (Graceful degradation)
- Cache: REQ-NFR-001, REQ-NFR-002 (Performance)
- Bedrock Models: REQ-SIM-006, REQ-RES-003, REQ-PLAN-001
- SageMaker Models: REQ-BURN-016, REQ-PROG-001
- Validator: REQ-NFR-055, REQ-NFR-058 (Data quality)

---

## 4. Resume Intelligence Pipeline

### 4.1 Resume Processing Data Flow

```mermaid
graph LR
    subgraph "Input Stage"
        UPLOAD[Resume Upload<br/>PDF/DOCX/TXT<br/>Max 5MB]
    end
    
    subgraph "Parsing Stage"
        TEXTRACT[Amazon Textract<br/>Extract Text]
        PARSER[Resume Parser<br/>Extract Sections]
    end
    
    subgraph "Analysis Stage"
        ATS[ATS Checker<br/>Format & Keywords]
        CONTENT[Content Analyzer<br/>Bullet Scoring]
        ALIGNMENT[Role Alignment<br/>Skill Matching]
    end
    
    subgraph "AI Enhancement"
        BEDROCK[Bedrock Claude<br/>Generate Suggestions]
        OPENSEARCH[OpenSearch<br/>Job Market Data]
    end
    
    subgraph "Scoring Stage"
        SCORER[Score Aggregator<br/>Weighted Average]
        IMPACT[Impact Calculator<br/>Career Impact]
    end
    
    subgraph "Output Stage"
        RESULT[Resume Analysis<br/>Score + Feedback]
        STORAGE[DynamoDB<br/>Save Analysis]
    end
    
    UPLOAD -->|REQ-SIM-001| TEXTRACT
    TEXTRACT -->|REQ-RES-001| PARSER
    
    PARSER -->|REQ-RES-005| ATS
    PARSER -->|REQ-RES-009| CONTENT
    PARSER -->|REQ-RES-014| ALIGNMENT
    
    ATS --> SCORER
    CONTENT --> BEDROCK
    ALIGNMENT --> OPENSEARCH
    
    BEDROCK -->|REQ-RES-010| SCORER
    OPENSEARCH -->|REQ-RES-015| SCORER
    
    SCORER -->|REQ-RES-003| IMPACT
    IMPACT -->|REQ-RES-018| RESULT
    
    RESULT -->|REQ-INT-010| STORAGE
    
    style UPLOAD fill:#E3F2FD
    style TEXTRACT fill:#FFA726
    style PARSER fill:#FFA726
    style ATS fill:#0066FF
    style CONTENT fill:#0066FF
    style ALIGNMENT fill:#0066FF
    style BEDROCK fill:#FF6F00
    style SCORER fill:#00C853
    style RESULT fill:#9C27B0
```

### 4.2 Resume Analysis Detailed Flow

```mermaid
sequenceDiagram
    participant User
    participant API as API Gateway
    participant Upload as Upload Lambda
    participant S3 as S3 Bucket
    participant Events as EventBridge
    participant Parser as Parser Lambda
    participant Textract as Amazon Textract
    participant Analyzer as Analysis Lambda
    participant Bedrock as Amazon Bedrock
    participant Search as OpenSearch
    participant DDB as DynamoDB
    
    Note over User,DDB: REQ-RES-001: Resume Upload & Parse
    
    User->>API: Upload Resume (PDF)
    API->>Upload: Invoke Handler
    
    Note over Upload: REQ-SIM-001: Validate<br/>Size < 5MB<br/>Type: PDF/DOCX/TXT
    
    Upload->>S3: Store Resume
    S3-->>Upload: S3 Key
    
    Upload->>DDB: Save Metadata
    Upload->>Events: Publish "Resume Uploaded"
    Upload-->>User: Upload Success
    
    Note over Events,Parser: REQ-RES-001: Async Parsing
    
    Events->>Parser: Trigger Parse
    Parser->>S3: Get Resume
    Parser->>Textract: Extract Text
    Textract-->>Parser: Parsed Text
    
    Parser->>Parser: Extract Sections<br/>- Contact Info<br/>- Education<br/>- Experience<br/>- Skills<br/>- Projects
    
    Parser->>DDB: Save Parsed Data
    Parser->>Events: Publish "Resume Parsed"
    
    Note over Events,Analyzer: REQ-RES-002: Analysis < 5s
    
    Events->>Analyzer: Trigger Analysis
    
    par ATS Compatibility Check
        Note over Analyzer: REQ-RES-005: ATS Check
        Analyzer->>Analyzer: Check Format<br/>Detect Issues<br/>Calculate Keyword Density
    and Content Quality Analysis
        Note over Analyzer: REQ-RES-009: Content Analysis
        Analyzer->>Bedrock: Analyze Bullets
        Bedrock-->>Analyzer: Bullet Scores
    and Role Alignment
        Note over Analyzer: REQ-RES-014: Role Alignment
        Analyzer->>Search: Query Job Descriptions
        Search-->>Analyzer: Required Skills
        Analyzer->>Analyzer: Calculate Alignment
    end
    
    Note over Analyzer: REQ-RES-003: Aggregate Scores<br/>ATS: 30%<br/>Content: 40%<br/>Alignment: 30%
    
    Analyzer->>Analyzer: Calculate Overall Score
    
    Note over Analyzer: REQ-RES-018: Impact Feedback
    Analyzer->>Analyzer: Generate Impact Analysis<br/>Career Impact Calculation
    
    Analyzer->>DDB: Save Analysis Result
    Analyzer->>Events: Publish "Resume Analyzed"
    
    Events->>User: Notify Analysis Complete
    
    User->>API: Get Analysis
    API->>DDB: Fetch Result
    DDB-->>API: Analysis Data
    API-->>User: Display Results
    
    Note over User: REQ-RES-004: Score Breakdown<br/>ATS: 65/100<br/>Content: 75/100<br/>Alignment: 76/100<br/>Overall: 72/100
```

**Requirements Mapping:**
- Upload: REQ-SIM-001, REQ-RES-001
- Parsing: REQ-RES-001, REQ-RES-002
- ATS Check: REQ-RES-005, REQ-RES-006, REQ-RES-007, REQ-RES-008
- Content Analysis: REQ-RES-009, REQ-RES-010, REQ-RES-011, REQ-RES-012
- Role Alignment: REQ-RES-014, REQ-RES-015, REQ-RES-016, REQ-RES-017
- Scoring: REQ-RES-003, REQ-RES-004
- Impact: REQ-RES-018, REQ-RES-019, REQ-RES-021

---

## 5. Burnout Detection & Recovery Flow

### 5.1 Low Mood Trigger to Plan Rebalancing

```mermaid
sequenceDiagram
    participant User
    participant UI as Mobile App
    participant API as API Gateway
    participant Mood as Mood Processor Lambda
    participant Comprehend as Amazon Comprehend
    participant Burnout as Burnout Monitor Lambda
    participant SageMaker as SageMaker Model
    participant DDB as DynamoDB
    participant Events as EventBridge
    participant Planner as Plan Rebalancer Lambda
    participant Notify as SNS Notifications
    
    Note over User,Notify: REQ-BURN-006: User Logs Mood
    
    User->>UI: Click "Log Mood"
    UI->>User: Show Mood Dialog
    User->>UI: Select Mood: 2/5<br/>Add Notes: "Feeling overwhelmed"
    
    Note over UI,API: REQ-BURN-006: Submit Mood Log
    
    UI->>API: POST /mood/log
    API->>Mood: Invoke Processor
    
    Note over Mood: REQ-BURN-007: Sentiment Analysis
    
    Mood->>Comprehend: Analyze Text Sentiment
    Comprehend-->>Mood: Sentiment: Negative<br/>Emotions: Stress, Overwhelm
    
    Note over Mood: REQ-BURN-008: Detect Stress Indicators
    
    Mood->>DDB: Save Mood Log
    Mood->>DDB: Get Last 7 Days Mood
    DDB-->>Mood: Mood History
    
    Note over Mood: REQ-BURN-009: Check Consecutive Low Days
    
    Mood->>Mood: Calculate:<br/>Day 1: 3<br/>Day 2: 2<br/>Day 3: 2 ← Today<br/>Consecutive Low: 2 days
    
    Mood->>Events: Publish "Mood Logged"
    
    Note over Events,Burnout: REQ-BURN-016: Calculate Burnout Risk
    
    Events->>Burnout: Trigger Risk Calculation
    
    Burnout->>DDB: Get User Data:<br/>- Study Hours (7-day avg)<br/>- Task Completion Rate<br/>- Mood Trend<br/>- Consistency
    
    DDB-->>Burnout: User Metrics
    
    Note over Burnout: REQ-BURN-016: Calculate Risk Factors
    
    Burnout->>SageMaker: Predict Burnout Risk
    SageMaker-->>Burnout: Risk Score: 72%
    
    Burnout->>Burnout: Aggregate Risk:<br/>Study Hours: 15%<br/>Completion: 10%<br/>Mood: 35%<br/>Consistency: 12%<br/>Total: 72%
    
    Note over Burnout: REQ-BURN-017: Check Threshold
    
    alt Burnout Risk > 70% OR Low Mood 3+ Days
        Note over Burnout: REQ-BURN-011: Activate Recovery Mode
        
        Burnout->>DDB: Update Burnout Index<br/>Risk: 72%<br/>Level: Critical
        
        Burnout->>Events: Publish "Recovery Mode Triggered"<br/>Reason: "Burnout risk 72%"
        
        Note over Events,Planner: REQ-BURN-011: Reduce Workload
        
        Events->>Planner: Trigger Recovery Mode
        
        Planner->>DDB: Get Current Study Plan
        DDB-->>Planner: Active Plan
        
        Note over Planner: REQ-BURN-012: Reduce Load by 50%
        
        Planner->>Planner: Rebalance Plan:<br/>- Remove intensive tasks<br/>- Keep only: reading, videos, review<br/>- Reduce daily hours by 50%<br/>- Max 4 hours/day
        
        Note over Planner: REQ-BURN-015: Extend Timeline
        
        Planner->>Planner: Extend Timeline:<br/>Original: 20 weeks<br/>Extended: 24 weeks<br/>+4 weeks buffer
        
        Note over Planner: REQ-BURN-013: Add Motivational Content
        
        Planner->>Planner: Add Messages:<br/>- Progress highlights<br/>- Achievements<br/>- Recovery tips
        
        Planner->>DDB: Save Recovery Plan<br/>Duration: 7 days<br/>Reduced Schedule
        
        Planner->>Events: Publish "Plan Rebalanced"
        
        Events->>Notify: Send Push Notification
        Notify->>UI: "Recovery Mode Activated"
        
        UI->>User: Show Recovery Banner<br/>Display Reduced Schedule<br/>Show Motivational Messages
        
        Note over User: REQ-BURN-012: Light Tasks Only<br/>REQ-BURN-013: Motivational Support
        
    else Normal Risk
        Burnout->>DDB: Update Burnout Index<br/>Risk: 28%<br/>Level: Safe
        
        Burnout-->>UI: Mood Logged Successfully
        UI->>User: Show Mood Trend
    end
    
    Note over User,UI: REQ-BURN-014: Exit Criteria<br/>Mood ≥ 3 for 3 consecutive days
```

**Requirements Mapping:**
- Mood Logging: REQ-BURN-006, REQ-BURN-007, REQ-BURN-008
- Consecutive Check: REQ-BURN-009
- Risk Calculation: REQ-BURN-016, REQ-BURN-017
- Recovery Activation: REQ-BURN-011, REQ-BURN-012, REQ-BURN-013
- Timeline Extension: REQ-BURN-015
- Exit Criteria: REQ-BURN-014
- Integration: REQ-INT-003

### 5.2 Burnout Risk Calculation Flow

```mermaid
graph TB
    subgraph "Input Data Collection"
        HOURS[Study Hours<br/>7-Day Average]
        COMPLETION[Task Completion<br/>Rate & Trend]
        MOOD[Mood Logs<br/>Last 7 Days]
        CONSISTENCY[Activity<br/>Consistency]
    end
    
    subgraph "Factor Analysis"
        F1[Study Hours Factor<br/>Threshold: 8hrs/day<br/>Weight: 25%]
        F2[Completion Factor<br/>Drop Detection<br/>Weight: 25%]
        F3[Mood Factor<br/>Trend Analysis<br/>Weight: 35%]
        F4[Consistency Factor<br/>Pattern Detection<br/>Weight: 15%]
    end
    
    subgraph "ML Prediction"
        SAGEMAKER[SageMaker Model<br/>XGBoost Classifier]
    end
    
    subgraph "Risk Aggregation"
        AGGREGATE[Risk Aggregator<br/>Weighted Sum]
        THRESHOLD[Threshold Check<br/>> 70% = Critical]
    end
    
    subgraph "Decision Engine"
        DECISION{Risk Level?}
        NORMAL[Normal Operation<br/>Continue Plan]
        WARNING[Warning State<br/>Reduce Intensity]
        CRITICAL[Critical State<br/>Recovery Mode]
    end
    
    subgraph "Actions"
        A1[Monitor Only]
        A2[Reduce Task Load 20%]
        A3[Activate Recovery Mode<br/>Reduce Load 50%]
    end
    
    HOURS --> F1
    COMPLETION --> F2
    MOOD --> F3
    CONSISTENCY --> F4
    
    F1 --> SAGEMAKER
    F2 --> SAGEMAKER
    F3 --> SAGEMAKER
    F4 --> SAGEMAKER
    
    SAGEMAKER --> AGGREGATE
    AGGREGATE --> THRESHOLD
    THRESHOLD --> DECISION
    
    DECISION -->|0-50%| NORMAL
    DECISION -->|51-70%| WARNING
    DECISION -->|71-100%| CRITICAL
    
    NORMAL --> A1
    WARNING --> A2
    CRITICAL --> A3
    
    style F3 fill:#FF5252
    style CRITICAL fill:#FF5252
    style A3 fill:#FF5252
    style WARNING fill:#FFA726
    style A2 fill:#FFA726
    style NORMAL fill:#00C853
    style A1 fill:#00C853
```

**Requirements Mapping:**
- Study Hours: REQ-BURN-001, REQ-BURN-002, REQ-BURN-003
- Completion: REQ-BURN-004
- Mood: REQ-BURN-006, REQ-BURN-007, REQ-BURN-008, REQ-BURN-009
- Risk Calculation: REQ-BURN-016
- Threshold: REQ-BURN-017
- Actions: REQ-BURN-011, REQ-BURN-018

---

## 6. Data Flow Diagrams

### 6.1 Career Trajectory Generation Flow

```mermaid
graph TB
    subgraph "User Input"
        RESUME[Resume Data]
        PROFILE[User Profile<br/>Target Role<br/>Learning Speed<br/>Weekly Hours]
    end
    
    subgraph "Data Enrichment"
        PARSE[Parse Resume<br/>Extract Skills]
        MARKET[Fetch Market Data<br/>Job Descriptions<br/>Hiring Trends]
    end
    
    subgraph "Skill Analysis"
        CURRENT[Current Skills<br/>Proficiency Levels]
        TARGET[Target Role Skills<br/>Requirements]
        GAP[Skill Gap Analysis<br/>Missing Skills<br/>Impact Scores]
    end
    
    subgraph "AI Generation"
        BEDROCK[Bedrock Claude<br/>Generate Scenarios]
        VALIDATE[Validate Output<br/>Check Consistency]
    end
    
    subgraph "Scenario Calculation"
        BEST[Best Case<br/>All Skills + Internships]
        AVG[Average Case<br/>Core Skills Only]
        WORST[Worst Case<br/>Minimal Progress]
    end
    
    subgraph "Risk Assessment"
        STAGNATION[Stagnation Risk<br/>Skill Acquisition Rate]
        BURNOUT_RISK[Burnout Probability<br/>Workload Analysis]
        FEASIBILITY[Goal Feasibility<br/>Time vs Requirements]
    end
    
    subgraph "Output Generation"
        TRAJECTORY[5-Year Trajectory<br/>Interview Success Rates<br/>Job Offer Probability]
        CONFIDENCE[Confidence Intervals<br/>±10% Range]
    end
    
    RESUME --> PARSE
    PROFILE --> MARKET
    
    PARSE --> CURRENT
    MARKET --> TARGET
    
    CURRENT --> GAP
    TARGET --> GAP
    
    GAP --> BEDROCK
    PROFILE --> BEDROCK
    
    BEDROCK --> VALIDATE
    
    VALIDATE --> BEST
    VALIDATE --> AVG
    VALIDATE --> WORST
    
    BEST --> STAGNATION
    AVG --> STAGNATION
    WORST --> STAGNATION
    
    PROFILE --> BURNOUT_RISK
    GAP --> FEASIBILITY
    
    BEST --> TRAJECTORY
    AVG --> TRAJECTORY
    WORST --> TRAJECTORY
    
    STAGNATION --> TRAJECTORY
    BURNOUT_RISK --> TRAJECTORY
    FEASIBILITY --> TRAJECTORY
    
    TRAJECTORY --> CONFIDENCE
    
    style RESUME fill:#E3F2FD
    style BEDROCK fill:#FF6F00
    style TRAJECTORY fill:#9C27B0
    style GAP fill:#0066FF
```

**Requirements Mapping:**
- Input: REQ-SIM-001, REQ-SIM-002, REQ-SIM-003
- Validation: REQ-SIM-004
- Skill Gap: REQ-SIM-011, REQ-SIM-012, REQ-SIM-013
- Generation: REQ-SIM-006, REQ-SIM-007, REQ-SIM-008
- Risk: REQ-SIM-016, REQ-SIM-017, REQ-SIM-018
- Feasibility: REQ-SIM-019
- Confidence: REQ-SIM-010

### 6.2 Study Plan Generation & Adaptation Flow

```mermaid
graph TB
    subgraph "Input Sources"
        SKILLS[Skill Gaps<br/>From Trajectory]
        CONSTRAINTS[User Constraints<br/>Weekly Hours<br/>Rest Days]
        VELOCITY[Learning Velocity<br/>Historical Data]
    end
    
    subgraph "Plan Generation"
        ROADMAP[Generate Roadmap<br/>Weekly Schedules]
        TASKS[Break Into Tasks<br/>Daily Assignments]
        RESOURCES[Recommend Resources<br/>Courses, Tutorials]
    end
    
    subgraph "Validation"
        HOURS_CHECK[Validate Hours<br/>≤ Weekly Limit * 1.1]
        DEPENDENCY[Check Dependencies<br/>Prerequisite Skills]
    end
    
    subgraph "Execution Monitoring"
        TRACK[Track Completion<br/>Task Status]
        VELOCITY_CALC[Calculate Velocity<br/>Skills/Time]
    end
    
    subgraph "Adaptation Triggers"
        SKIP[Task Skipped]
        LOW_COMPLETION[Completion < 60%]
        CONTEXT_CHANGE[Exam/Interview]
        BURNOUT_ALERT[Burnout Risk High]
    end
    
    subgraph "Rebalancing Logic"
        REDISTRIBUTE[Redistribute Tasks<br/>Across Weeks]
        ADJUST_INTENSITY[Adjust Intensity<br/>±20%]
        EXTEND_TIMELINE[Extend Timeline<br/>Add Weeks]
    end
    
    subgraph "Output"
        UPDATED_PLAN[Updated Study Plan<br/>Rebalanced Schedule]
        NOTIFICATIONS[User Notifications<br/>Plan Changes]
    end
    
    SKILLS --> ROADMAP
    CONSTRAINTS --> ROADMAP
    VELOCITY --> ROADMAP
    
    ROADMAP --> TASKS
    TASKS --> RESOURCES
    
    RESOURCES --> HOURS_CHECK
    HOURS_CHECK --> DEPENDENCY
    
    DEPENDENCY -->|Valid| TRACK
    HOURS_CHECK -->|Exceeded| EXTEND_TIMELINE
    
    TRACK --> VELOCITY_CALC
    
    TRACK --> SKIP
    TRACK --> LOW_COMPLETION
    TRACK --> CONTEXT_CHANGE
    VELOCITY_CALC --> BURNOUT_ALERT
    
    SKIP --> REDISTRIBUTE
    LOW_COMPLETION --> ADJUST_INTENSITY
    CONTEXT_CHANGE --> REDISTRIBUTE
    BURNOUT_ALERT --> ADJUST_INTENSITY
    
    REDISTRIBUTE --> HOURS_CHECK
    ADJUST_INTENSITY --> HOURS_CHECK
    EXTEND_TIMELINE --> UPDATED_PLAN
    
    UPDATED_PLAN --> NOTIFICATIONS
    
    style SKIP fill:#FFA726
    style BURNOUT_ALERT fill:#FF5252
    style REDISTRIBUTE fill:#00C853
    style UPDATED_PLAN fill:#9C27B0
```

**Requirements Mapping:**
- Generation: REQ-PLAN-001, REQ-PLAN-002, REQ-PLAN-003, REQ-PLAN-004
- Constraints: REQ-PLAN-005, REQ-PLAN-006
- Resources: REQ-PLAN-007, REQ-PLAN-008, REQ-PLAN-009
- Tracking: REQ-PLAN-012
- Rebalancing: REQ-PLAN-013, REQ-PLAN-015, REQ-PLAN-016
- Context: REQ-PLAN-014, REQ-PLAN-019
- Integration: REQ-INT-002, REQ-INT-003

---

## 7. Component Interaction Diagrams

### 7.1 Cross-Module Integration Flow

```mermaid
sequenceDiagram
    participant User
    participant Resume as Resume Engine
    participant Career as Career Engine
    participant Planner as Planner Engine
    participant Burnout as Burnout Engine
    participant Progress as Progress Engine
    participant Events as EventBridge
    participant DDB as DynamoDB
    
    Note over User,DDB: REQ-INT-001: Resume → Career Integration
    
    User->>Resume: Upload Resume
    Resume->>Resume: Analyze Resume
    Resume->>DDB: Save Analysis
    Resume->>Events: Publish "Resume Analyzed"
    
    Events->>Career: Trigger Simulation
    Career->>DDB: Get Resume Data
    Career->>Career: Generate Trajectory<br/>Using Resume Skills
    Career->>DDB: Save Trajectory
    Career->>Events: Publish "Trajectory Generated"
    
    Note over Events,Planner: REQ-INT-002: Career → Planner Integration
    
    Events->>Planner: Trigger Plan Generation
    Planner->>DDB: Get Skill Gaps
    Planner->>Planner: Generate Study Plan<br/>Based on Gaps
    Planner->>DDB: Save Plan
    Planner->>Events: Publish "Plan Generated"
    
    Note over User,Progress: User Executes Plan
    
    User->>Planner: Complete Task
    Planner->>DDB: Update Task Status
    Planner->>Progress: Update Metrics
    Progress->>Progress: Calculate Velocity
    Progress->>DDB: Save Progress
    
    Note over User,Burnout: REQ-INT-003: Burnout → Planner Integration
    
    User->>Burnout: Log Low Mood
    Burnout->>Burnout: Calculate Risk
    
    alt Risk > 70%
        Burnout->>Events: Publish "Burnout Alert"
        Events->>Planner: Trigger Rebalance
        Planner->>DDB: Get Current Plan
        Planner->>Planner: Reduce Intensity 50%
        Planner->>DDB: Save Updated Plan
        Planner->>User: Notify Plan Changed
    end
    
    Note over User,Career: REQ-INT-004: Resume Update → Trajectory Recalc
    
    User->>Resume: Update Resume
    Resume->>Resume: Re-analyze
    Resume->>DDB: Save New Version
    Resume->>Events: Publish "Resume Updated"
    
    Events->>Career: Trigger Recalculation
    Career->>DDB: Get Updated Resume
    Career->>Career: Regenerate Trajectory
    Career->>DDB: Save New Trajectory
    Career->>User: Notify Trajectory Updated
    
    Note over User: REQ-INT-009: Event Processing < 5s
```

**Requirements Mapping:**
- Resume → Career: REQ-INT-001
- Career → Planner: REQ-INT-002
- Burnout → Planner: REQ-INT-003
- Resume Update: REQ-INT-004
- Event Processing: REQ-INT-009

### 7.2 Feedback Loop Architecture

```mermaid
graph TB
    subgraph "Daily Feedback Loop"
        TASK_COMPLETE[Task Completion]
        PROGRESS_UPDATE[Progress Update]
        VELOCITY_CALC[Velocity Calculation]
        PLAN_ADJUST[Plan Micro-Adjustment]
    end
    
    subgraph "Weekly Feedback Loop"
        WEEK_REVIEW[Weekly Review]
        VELOCITY_ANALYSIS[Velocity Analysis]
        ROADMAP_REFINE[Roadmap Refinement]
        RESOURCE_OPT[Resource Optimization]
    end
    
    subgraph "Monthly Feedback Loop"
        SKILL_ASSESS[Skill Assessment]
        RESUME_UPDATE[Resume Update]
        CAREER_RESIM[Career Re-simulation]
        GOAL_ADJUST[Goal Adjustment]
    end
    
    subgraph "Real-Time Feedback"
        MOOD_LOG[Mood Logging]
        BURNOUT_CALC[Burnout Calculation]
        RECOVERY_TRIGGER[Recovery Mode]
        INTENSITY_REDUCE[Intensity Reduction]
    end
    
    TASK_COMPLETE --> PROGRESS_UPDATE
    PROGRESS_UPDATE --> VELOCITY_CALC
    VELOCITY_CALC --> PLAN_ADJUST
    PLAN_ADJUST --> TASK_COMPLETE
    
    PLAN_ADJUST --> WEEK_REVIEW
    WEEK_REVIEW --> VELOCITY_ANALYSIS
    VELOCITY_ANALYSIS --> ROADMAP_REFINE
    ROADMAP_REFINE --> RESOURCE_OPT
    RESOURCE_OPT --> PLAN_ADJUST
    
    ROADMAP_REFINE --> SKILL_ASSESS
    SKILL_ASSESS --> RESUME_UPDATE
    RESUME_UPDATE --> CAREER_RESIM
    CAREER_RESIM --> GOAL_ADJUST
    GOAL_ADJUST --> ROADMAP_REFINE
    
    MOOD_LOG --> BURNOUT_CALC
    BURNOUT_CALC --> RECOVERY_TRIGGER
    RECOVERY_TRIGGER --> INTENSITY_REDUCE
    INTENSITY_REDUCE --> PLAN_ADJUST
    
    style TASK_COMPLETE fill:#00C853
    style MOOD_LOG fill:#FF5252
    style BURNOUT_CALC fill:#FF5252
    style RECOVERY_TRIGGER fill:#FF5252
```

**Requirements Mapping:**
- Daily Loop: REQ-INT-006
- Weekly Loop: REQ-INT-007
- Monthly Loop: REQ-INT-008
- Real-Time: REQ-INT-003, REQ-BURN-016

---

## 8. Deployment Architecture

### 8.1 Multi-Environment Deployment

```mermaid
graph TB
    subgraph "Development Environment"
        DEV_VPC[VPC - Dev]
        DEV_LAMBDA[Lambda Functions]
        DEV_DDB[DynamoDB - On-Demand]
        DEV_S3[S3 Buckets]
    end
    
    subgraph "Staging Environment"
        STAGE_VPC[VPC - Staging]
        STAGE_LAMBDA[Lambda Functions]
        STAGE_DDB[DynamoDB - Provisioned]
        STAGE_S3[S3 Buckets]
        STAGE_TEST[Load Testing]
    end
    
    subgraph "Production Environment - Primary"
        PROD_VPC[VPC - ap-south-1]
        PROD_LAMBDA[Lambda Functions<br/>Auto-scaling]
        PROD_DDB[DynamoDB<br/>Global Tables]
        PROD_S3[S3 Buckets<br/>Versioning]
        PROD_CF[CloudFront]
        PROD_R53[Route53]
    end
    
    subgraph "Production Environment - DR"
        DR_VPC[VPC - ap-southeast-1]
        DR_LAMBDA[Lambda Functions<br/>Standby]
        DR_DDB[DynamoDB<br/>Replica]
        DR_S3[S3 Buckets<br/>Replication]
    end
    
    subgraph "CI/CD Pipeline"
        GITHUB[GitHub Actions]
        TEST[Automated Tests]
        CDK[AWS CDK Deploy]
    end
    
    subgraph "Monitoring"
        CW[CloudWatch]
        XRAY[X-Ray]
        ALARMS[CloudWatch Alarms]
    end
    
    GITHUB --> TEST
    TEST --> CDK
    
    CDK -->|Deploy| DEV_LAMBDA
    CDK -->|Deploy| STAGE_LAMBDA
    CDK -->|Deploy| PROD_LAMBDA
    
    STAGE_TEST --> STAGE_LAMBDA
    
    PROD_R53 --> PROD_CF
    PROD_CF --> PROD_LAMBDA
    PROD_LAMBDA --> PROD_DDB
    PROD_LAMBDA --> PROD_S3
    
    PROD_DDB -.->|Replicate| DR_DDB
    PROD_S3 -.->|Replicate| DR_S3
    
    PROD_R53 -.->|Failover| DR_LAMBDA
    
    PROD_LAMBDA --> CW
    PROD_LAMBDA --> XRAY
    CW --> ALARMS
    
    style PROD_VPC fill:#00C853
    style DR_VPC fill:#FFA726
    style DEV_VPC fill:#E3F2FD
```

**Requirements Mapping:**
- Multi-Environment: REQ-NFR-051
- Auto-scaling: REQ-NFR-007, REQ-NFR-008
- Backup: REQ-NFR-013
- Monitoring: REQ-NFR-014, REQ-NFR-048, REQ-NFR-049
- Disaster Recovery: RTO 4 hours, RPO 1 hour

### 8.2 Security Architecture

```mermaid
graph TB
    subgraph "Edge Security"
        WAF[AWS WAF<br/>SQL Injection<br/>XSS Protection<br/>Rate Limiting]
        SHIELD[AWS Shield<br/>DDoS Protection]
    end
    
    subgraph "Authentication & Authorization"
        COGNITO[Amazon Cognito<br/>User Pool]
        IAM[IAM Roles<br/>Least Privilege]
    end
    
    subgraph "Network Security"
        VPC[VPC<br/>Private Subnets]
        SG[Security Groups<br/>Restricted Access]
        NACL[Network ACLs]
    end
    
    subgraph "Data Security"
        KMS[AWS KMS<br/>Encryption Keys]
        DDB_ENC[DynamoDB<br/>Encryption at Rest]
        S3_ENC[S3<br/>Encryption at Rest]
        TLS[TLS 1.3<br/>Encryption in Transit]
    end
    
    subgraph "Application Security"
        INPUT_VAL[Input Validation<br/>Zod Schemas]
        SANITIZE[Input Sanitization<br/>XSS Prevention]
        SECRETS[Secrets Manager<br/>API Keys]
    end
    
    subgraph "Monitoring & Audit"
        CLOUDTRAIL[CloudTrail<br/>API Audit Logs]
        GUARDDUTY[GuardDuty<br/>Threat Detection]
        LOGS[CloudWatch Logs<br/>Security Events]
    end
    
    WAF --> COGNITO
    SHIELD --> WAF
    
    COGNITO --> IAM
    IAM --> VPC
    
    VPC --> SG
    SG --> NACL
    
    KMS --> DDB_ENC
    KMS --> S3_ENC
    
    TLS --> INPUT_VAL
    INPUT_VAL --> SANITIZE
    SANITIZE --> SECRETS
    
    IAM --> CLOUDTRAIL
    VPC --> GUARDDUTY
    SANITIZE --> LOGS
    
    style WAF fill:#FF5252
    style KMS fill:#9C27B0
    style COGNITO fill:#0066FF
```

**Requirements Mapping:**
- WAF: REQ-NFR-022
- Authentication: REQ-NFR-019, REQ-NFR-020
- Rate Limiting: REQ-NFR-021
- Encryption at Rest: REQ-NFR-017
- Encryption in Transit: REQ-NFR-018
- IAM: REQ-NFR-023
- Audit Logs: REQ-NFR-024
- Input Validation: REQ-NFR-053

---

## 9. Performance & Scalability Architecture

### 9.1 Caching Strategy

```mermaid
graph TB
    subgraph "Client-Side Cache"
        BROWSER[Browser Cache<br/>Static Assets]
        LOCAL[LocalStorage<br/>User Preferences]
    end
    
    subgraph "CDN Cache"
        CLOUDFRONT[CloudFront<br/>Edge Locations<br/>TTL: 24 hours]
    end
    
    subgraph "Application Cache"
        REDIS[ElastiCache Redis<br/>Session & Data Cache]
    end
    
    subgraph "Cache Layers"
        L1[L1: Trajectory Cache<br/>TTL: 24 hours]
        L2[L2: Resume Analysis<br/>TTL: 1 hour]
        L3[L3: Market Data<br/>TTL: 7 days]
        L4[L4: User Session<br/>TTL: 30 minutes]
    end
    
    subgraph "Database"
        DDB[DynamoDB<br/>Source of Truth]
    end
    
    BROWSER --> CLOUDFRONT
    LOCAL --> CLOUDFRONT
    
    CLOUDFRONT --> REDIS
    
    REDIS --> L1
    REDIS --> L2
    REDIS --> L3
    REDIS --> L4
    
    L1 -.->|Cache Miss| DDB
    L2 -.->|Cache Miss| DDB
    L3 -.->|Cache Miss| DDB
    L4 -.->|Cache Miss| DDB
    
    DDB -.->|Update| L1
    DDB -.->|Update| L2
    DDB -.->|Update| L3
    
    style CLOUDFRONT fill:#FFA726
    style REDIS fill:#FF5252
    style DDB fill:#0066FF
```

**Requirements Mapping:**
- CDN: REQ-NFR-045, REQ-NFR-046
- Redis: REQ-NFR-003, REQ-NFR-005
- Cache Strategy: REQ-NFR-010

### 9.2 Auto-Scaling Configuration

```mermaid
graph LR
    subgraph "Load Metrics"
        CPU[CPU Utilization]
        MEMORY[Memory Usage]
        REQUESTS[Request Count]
        LATENCY[Response Latency]
    end
    
    subgraph "Scaling Triggers"
        SCALE_UP[Scale Up<br/>CPU > 70%<br/>Latency > 1s]
        SCALE_DOWN[Scale Down<br/>CPU < 30%<br/>Low Traffic]
    end
    
    subgraph "Lambda Scaling"
        LAMBDA_POOL[Lambda Pool<br/>Reserved: 10<br/>Max: 1000]
    end
    
    subgraph "DynamoDB Scaling"
        DDB_AUTO[Auto-Scaling<br/>Target: 70%<br/>Min: 50 RCU/WCU<br/>Max: 500 RCU/WCU]
    end
    
    CPU --> SCALE_UP
    MEMORY --> SCALE_UP
    REQUESTS --> SCALE_UP
    LATENCY --> SCALE_UP
    
    CPU --> SCALE_DOWN
    REQUESTS --> SCALE_DOWN
    
    SCALE_UP --> LAMBDA_POOL
    SCALE_UP --> DDB_AUTO
    
    SCALE_DOWN --> LAMBDA_POOL
    SCALE_DOWN --> DDB_AUTO
    
    style SCALE_UP fill:#FF5252
    style SCALE_DOWN fill:#00C853
```

**Requirements Mapping:**
- Lambda Scaling: REQ-NFR-007, REQ-NFR-008
- DynamoDB Scaling: REQ-NFR-009
- Performance: REQ-NFR-001, REQ-NFR-002, REQ-NFR-003

---

## 10. Data Model Architecture

### 10.1 DynamoDB Single-Table Design

```mermaid
graph TB
    subgraph "Access Patterns"
        AP1[Get User Profile]
        AP2[Get Career Trajectory]
        AP3[Get Resume Analysis]
        AP4[Get Study Plan]
        AP5[Get Daily Tasks]
        AP6[Get Mood Logs]
        AP7[Get Progress Metrics]
    end
    
    subgraph "DynamoDB Table: FutureForge-Main"
        PK[Partition Key: PK]
        SK[Sort Key: SK]
        GSI1[GSI1: Date-based queries]
        GSI2[GSI2: Status-based queries]
    end
    
    subgraph "Item Types"
        USER[USER#userId<br/>PROFILE]
        TRAJ[USER#userId<br/>TRAJECTORY#timestamp]
        RES[USER#userId<br/>RESUME#version]
        PLAN[USER#userId<br/>PLAN#planId]
        TASK[USER#userId<br/>TASK#date#taskId]
        MOOD[USER#userId<br/>MOOD#date]
        PROG[USER#userId<br/>PROGRESS#date]
        BURN[USER#userId<br/>BURNOUT#CURRENT]
    end
    
    AP1 --> PK
    AP2 --> PK
    AP3 --> PK
    AP4 --> PK
    AP5 --> GSI1
    AP6 --> GSI1
    AP7 --> GSI1
    
    PK --> USER
    PK --> TRAJ
    PK --> RES
    PK --> PLAN
    PK --> TASK
    PK --> MOOD
    PK --> PROG
    PK --> BURN
    
    SK --> USER
    SK --> TRAJ
    SK --> RES
    SK --> PLAN
    SK --> TASK
    SK --> MOOD
    SK --> PROG
    SK --> BURN
    
    style PK fill:#0066FF
    style SK fill:#0066FF
    style GSI1 fill:#00C853
```

**Requirements Mapping:**
- Single Table: REQ-INT-010
- Access Patterns: REQ-NFR-009
- TTL: Auto-expire old data

### 10.2 Event-Driven Architecture

```mermaid
graph LR
    subgraph "Event Producers"
        RESUME_SVC[Resume Service]
        CAREER_SVC[Career Service]
        PLANNER_SVC[Planner Service]
        BURNOUT_SVC[Burnout Service]
    end
    
    subgraph "EventBridge"
        BUS[Event Bus<br/>futureforge-events]
    end
    
    subgraph "Event Rules"
        R1[Resume Uploaded]
        R2[Resume Analyzed]
        R3[Trajectory Generated]
        R4[Plan Generated]
        R5[Burnout Alert]
        R6[Task Completed]
    end
    
    subgraph "Event Consumers"
        PARSER[Parser Lambda]
        ANALYZER[Analyzer Lambda]
        SIMULATOR[Simulator Lambda]
        REBALANCER[Rebalancer Lambda]
        NOTIFIER[Notifier Lambda]
    end
    
    subgraph "Dead Letter Queue"
        DLQ[SQS DLQ<br/>Failed Events]
    end
    
    RESUME_SVC --> BUS
    CAREER_SVC --> BUS
    PLANNER_SVC --> BUS
    BURNOUT_SVC --> BUS
    
    BUS --> R1
    BUS --> R2
    BUS --> R3
    BUS --> R4
    BUS --> R5
    BUS --> R6
    
    R1 --> PARSER
    R2 --> ANALYZER
    R3 --> SIMULATOR
    R4 --> REBALANCER
    R5 --> REBALANCER
    R6 --> NOTIFIER
    
    PARSER -.->|Failure| DLQ
    ANALYZER -.->|Failure| DLQ
    SIMULATOR -.->|Failure| DLQ
    REBALANCER -.->|Failure| DLQ
    
    style BUS fill:#FFA726
    style DLQ fill:#FF5252
```

**Requirements Mapping:**
- Event Bus: REQ-INT-009
- Async Processing: REQ-NFR-011
- DLQ: REQ-NFR-014
- Retry: 3 attempts, 2hr max age

---

## 11. Requirements Traceability Matrix

### 11.1 Architecture Component to Requirements Mapping

| Component | Requirements | Diagram Reference |
|-----------|-------------|-------------------|
| **API Gateway** | REQ-NFR-003, REQ-NFR-021 | Section 2.1 |
| **Cognito** | REQ-NFR-019, REQ-NFR-020 | Section 2.1, 8.2 |
| **Lambda Functions** | REQ-NFR-001, REQ-NFR-002, REQ-NFR-006 | Section 2.1 |
| **Bedrock** | REQ-SIM-006, REQ-RES-003, REQ-PLAN-001 | Section 3.1 |
| **SageMaker** | REQ-BURN-016, REQ-PROG-001 | Section 3.1 |
| **Comprehend** | REQ-BURN-007, REQ-BURN-008 | Section 3.1 |
| **Textract** | REQ-RES-001, REQ-SIM-001 | Section 4.1 |
| **DynamoDB** | REQ-INT-010, REQ-NFR-013, REQ-NFR-017 | Section 2.1, 10.1 |
| **S3** | REQ-SIM-001, REQ-NFR-017 | Section 2.1 |
| **ElastiCache** | REQ-NFR-003, REQ-NFR-010 | Section 9.1 |
| **OpenSearch** | REQ-RES-015, REQ-SIM-009 | Section 2.1 |
| **EventBridge** | REQ-INT-009, REQ-NFR-014 | Section 2.1, 10.2 |
| **Step Functions** | REQ-INT-004 | Section 2.1 |
| **CloudWatch** | REQ-NFR-048, REQ-NFR-049 | Section 2.1 |
| **X-Ray** | REQ-NFR-049 | Section 2.1 |
| **WAF** | REQ-NFR-022 | Section 8.2 |
| **KMS** | REQ-NFR-017, REQ-NFR-018 | Section 8.2 |

### 11.2 Flow to Requirements Mapping

| Flow | Requirements | Diagram Reference |
|------|-------------|-------------------|
| **Resume Upload** | REQ-SIM-001, REQ-RES-001 | Section 4.2 |
| **Resume Analysis** | REQ-RES-002 to REQ-RES-021 | Section 4.1, 4.2 |
| **Career Simulation** | REQ-SIM-006 to REQ-SIM-023 | Section 6.1 |
| **Study Plan Generation** | REQ-PLAN-001 to REQ-PLAN-023 | Section 6.2 |
| **Burnout Detection** | REQ-BURN-001 to REQ-BURN-023 | Section 5.1, 5.2 |
| **Plan Rebalancing** | REQ-PLAN-013, REQ-INT-003 | Section 5.1 |
| **Progress Tracking** | REQ-PROG-001 to REQ-PROG-025 | Section 7.1 |
| **Cross-Module Integration** | REQ-INT-001 to REQ-INT-009 | Section 7.1 |

---

## 12. Architecture Decision Records (ADRs)

### ADR-001: Serverless Architecture

**Decision:** Use AWS Lambda for all compute workloads

**Rationale:**
- Zero infrastructure management
- Pay-per-use pricing (cost-efficient for hackathon)
- Automatic scaling (REQ-NFR-007, REQ-NFR-008)
- Fast deployment and iteration

**Consequences:**
- Cold start latency (mitigated with provisioned concurrency)
- 15-minute execution limit (acceptable for our use case)
- Vendor lock-in to AWS

### ADR-002: Single-Table DynamoDB Design

**Decision:** Use single-table design for DynamoDB

**Rationale:**
- Efficient access patterns
- Lower cost (fewer tables)
- Better performance (fewer network calls)
- Follows AWS best practices

**Consequences:**
- More complex data modeling
- Requires careful GSI design
- Harder to understand for new developers

### ADR-003: Amazon Bedrock for AI

**Decision:** Use Amazon Bedrock instead of self-hosted models

**Rationale:**
- No model management overhead
- Multiple model options (Claude, Titan)
- Pay-per-token pricing
- Built-in safety features

**Consequences:**
- Higher per-request cost than self-hosted
- Limited customization
- Dependent on AWS model availability

### ADR-004: Event-Driven Architecture

**Decision:** Use EventBridge for inter-service communication

**Rationale:**
- Loose coupling between services (REQ-NFR-051)
- Async processing for heavy workloads
- Built-in retry and DLQ (REQ-NFR-014)
- Easy to add new consumers

**Consequences:**
- Eventual consistency
- More complex debugging
- Additional latency for async operations

---

## Appendix: Diagram Legend

### Color Coding

- 🔵 **Blue (#0066FF):** Core business logic components
- 🔴 **Red (#FF5252):** Burnout/Alert related components
- 🟢 **Green (#00C853):** Success/Progress related components
- 🟠 **Orange (#FFA726):** Processing/Transformation components
- 🟣 **Purple (#9C27B0):** Output/Result components
- ⚪ **Gray (#E3F2FD):** Input/User components

### Arrow Types

- **Solid Arrow (→):** Synchronous call
- **Dashed Arrow (-.->):** Asynchronous call
- **Thick Arrow (==>):** Data flow
- **Dotted Arrow (...>):** Optional/Conditional flow

---

**Document Version:** 1.0  
**Last Updated:** February 14, 2026  
**Status:** Complete  
**Target:** Amazon AI for Bharat Hackathon

*End of Architecture Diagrams Document*
