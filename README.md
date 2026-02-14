# 🏛️ AI for Bharat - FutureForge: Predict, Prepare, Perform

[![AWS](https://img.shields.io/badge/AWS-Cloud%20Native-orange?logo=amazon-aws)](https://aws.amazon.com/)
[![AI](https://img.shields.io/badge/AI-Powered-blue?logo=openai)](https://aws.amazon.com/bedrock/)

> **The AI Academic Survival System**  
> *Submitted for AWS AI for Bharat Hackathon 2026*

## 🌟 Overview

FutureForge AI is an AI-powered career growth platform that helps students and developers plan, improve, and track their professional journey. It combines career simulation, resume analysis, and adaptive study planning into one system that predicts future career paths, identifies skill gaps, and generates personalized roadmaps. By continuously adjusting based on user progress, workload, and goals, FutureForge AI turns career development into a data-driven, guided, and measurable process instead of guesswork.

### 🎯 Problem Statement

- Students lack clarity on which skills and actions actually improve their career outcomes.
- Existing tools for resumes, learning, and career guidance work separately and don’t give a complete growth picture.
- Many learners follow random study plans, leading to wasted effort and slow progress.
- Weak resumes and missing industry-aligned skills reduce internship and job opportunities.
- No system currently predicts realistic career paths while adapting to user progress and burnout risk.

### 💡 Solution

FutureForge AI is a unified platform that simulates career trajectories, analyzes resumes, and builds personalized skill roadmaps based on a user’s profile and goals. It continuously adapts study plans and recommendations using AI, helping learners make smarter decisions and grow in a structured, data-driven way.

## ✨ Key Features

### 🎯 Career Trajectory Simulation

- **ML Prediction Engine**: Simulates multiple 5-year career paths based on skills, resume data, and goals
- **Outcome Comparison**: Shows best-case vs average growth with estimated salary ranges
- **Cloud Processing**: Runs simulations on scalable AWS compute infrastructure
- **Impact Testing**: Demonstrates how internships, projects, or certifications change outcomes

### 📊 Skill Gap Analysis

- **NLP Skill Extraction**: Detects current skills directly from resume and profile inputs
- **Market Matching**: Compares user profile with industry demand datasets
- **Cloud Skill Database**: Uses hosted datasets and APIs stored on AWS cloud storage
- **Priority Roadmap**: Ranks skills by real career impact instead of generic suggestions

### 📄 AI Resume Intelligence

- **ATS Parsing**: Checks keywords, formatting, and recruiter compatibility using NLP
- **Role Alignment Score**: Measures how well the resume matches the target job role
- **Smart Suggestions**: Rewrites bullets, adds metrics, and recommends missing experience
- **Secure Processing**: Resume analysis handled via backend services deployed on AWS

### 📅 Adaptive Study Planner

- **Task Generation**: Converts skill gaps into weekly and daily action plans
- **Dynamic Adjustment**: Rebalances schedule if tasks are skipped or workload rises
- **Progress Sync**: Stores updates in real time using cloud-based tracking architecture
- **Integration Ready**: Designed to connect with calendars, reminders, and notification APIs

### 🔁 Closed-Loop Growth System

- **Continuous Re-Evaluation**: Updates predictions whenever user progress changes
- **Module Integration**: Resume, skills, and planner all feed into one growth engine
- **Centralized Profile**: Uses scalable cloud databases to store long-term user data
- **Long-Term Tracking**: Focuses on continuous improvement instead of one-time analysis

### ⚠️ Burnout Risk Detection

- **Workload Monitoring**: Tracks study hours, skipped tasks, and schedule intensity
- **Pattern Analysis**: Uses AI + heuristic models to detect unhealthy productivity trends
- **Smart Recommendations**: Suggests lighter schedules or recovery periods automatically
- **Cloud Analytics Layer**: Runs monitoring pipelines on scalable backend infrastructure

## 🏗 Architecture

### AWS Services Used

- **Amazon Bedrock** – Career simulation logic, resume NLP analysis, and roadmap generation using foundation models
- **AWS Lambda** – Serverless backend for processing resumes, generating plans, and running prediction workflows
- **Amazon S3** – Secure storage for resumes, user reports, and generated learning plans
- **Amazon DynamoDB** – Stores user profiles, skill data, and progress tracking in real time
- **Amazon API Gateway** – Handles frontend ↔ backend communication through REST APIs
- **Amazon CloudWatch** – Monitoring usage, workload patterns, and burnout detection signals
- **AWS Cognito** – User authentication and secure login system
- **Amazon EventBridge** – Triggers automatic plan updates when user data changes

### Architecture Highlights

- **AI-Centric Pipeline**: Resume → Skill Analysis → Career Simulation → Planner runs as a connected workflow
- **Serverless & Scalable**: Lambda-based processing allows handling many users without manual scaling
- **Real-Time Adaptation**: Event-driven updates instantly adjust study plans and projections
- **Secure Data Flow**: Authentication, storage, and API layers ensure safe handling of user career data
- **Modular Design**: Each engine (Simulation, Resume, Planner) works independently but shares one profile database

## 📋 Project Structure

```
hack2skill_ai4bharat/
├── 📁 .kiro/specs/
│   ├── 📄 requirements.md          # 12 detailed requirements
│   ├── 📄 design.md                # AWS architecture & 37 correctness properties
│   └── 📄 tasks.md                 # Implementation plan with PBT
├── architecture-diagram.md         # Professional AWS architecture diagram
├── wireframes-preview.md           # Complete mobile app wireframes
├── wireframes.md                   # wireframes
└── 📄 README.md                   # This file
```

## Quick Start

## 1.Clone the repository

- git clone https:/Shiv-15CC/github.com//hack2skill_ai4bharat.git
- cd hack2skill_ai4bharat

## 2.Review the documentation

- Read [requirements.md](https://github.com/Shiv-15CC/hack2skill_ai4bharat/blob/main/.kiro/specs/requirements.md) for detailed specifications  
- Check [design.md](https://github.com/Shiv-15CC/hack2skill_ai4bharat/blob/main/.kiro/specs/design.md) for architecture details  
- Follow [tasks.md](https://github.com/Shiv-15CC/hack2skill_ai4bharat/blob/main/.kiro/specs/tasks.md) for implementation plan


## 3.View wireframes and architecture

- **Open wireframes.html in your browser**
- **Open architecture.html for technical architecture**

## 🎨 Visual Design

## 🧪 Testing Strategy

### Model & Logic Testing
The project validates core system behavior through structured testing of AI outputs and workflow correctness:
- **Resume Parsing Accuracy** – Ensures skills, experience, and keywords are correctly extracted
- **Career Prediction Stability** – Checks that similar inputs produce consistent trajectory results
- **Skill Gap Detection Validity** – Confirms recommended skills match the selected target role
- **Planner Logic Testing** – Verifies study schedules adjust properly when tasks change or are skipped
- **Burnout Signal Checks** – Tests workload thresholds and alert conditions

### Testing Tools & Frameworks
- **Python Testing** – Unit tests for AI modules and backend logic
- **Frontend Testing** – Basic UI testing for dashboard, upload, and planner flows
- **API Testing** – Endpoint validation to ensure smooth frontend-backend communication
- **AWS Integration Tests** – Mocked storage, authentication, and serverless function responses

## 🌍 Multilingual Support

### Supported Languages
- 🇬🇧 **English** - International accessibility
- 🇮🇳 **हिंदी (Hindi)** - Uttar Pradesh, Bihar, Madhya Pradesh, Rajasthan & others
- 🇮🇳 **తెలుగు (Telugu)** - Andhra Pradesh & Telangana
- 🇮🇳 **বাংলা (Bengali)** - West Bengal
- 🇮🇳 **ગુજરાતી (Gujarati)** - Gujarat culture
- 🇮🇳 **ಕನ್ನಡ (Kannada)** - Karnataka traditions
- 🇮🇳 **മലയാളം (Malayalam)** - Kerala heritage
- 🇮🇳 **मराठी (Marathi)** - Maharashtra heritage
- 🇮🇳 **ਪੰਜਾਬੀ (Punjabi)** - Punjab culture
- 🇮🇳 **தமிழ் (Tamil)** - Tamil Nadu

## 🔐 Security & Privacy

### Data Protection
- **Encrypted storage for resumes and user profiles using secure cloud storage layers**
- **Secure data transfer via HTTPS APIs between frontend, backend, and AI services**
- **AWS access control to restrict unauthorized data access and ensure safe processing**
- **Minimal data retention policy to store only what is required for simulations**

### User Privacy
- **User-controlled uploads — resumes and inputs are processed only after consent**
- **Profile-based analysis without collecting unnecessary personal information**
- **Session-safe authentication using secure login and token validation**
- **Transparent usage — users know what data is used for predictions and planning**

## 🏆 Hackathon Submission

- **Team**: QuadVanta
- **Category**: AI for Learning & Developer Productivity
- **Submission Date**: February 2026
- **Repository**: [github.com/Shivam-15CC/hack2skill_ai4bharat](https://github.com/Shiv-15CC/hack2skill_ai4bharat)

## 💡 Innovation Highlights

- **Unified Growth Engine** – Combines career simulation, resume intelligence, and study planning into one AI system
- **Future Outcome Prediction** – Shows realistic career paths and salary impact instead of generic advice
- **Closed-Loop Learning Design** – Resume → Skills → Planner → Re-simulation creates continuous improvement cycle
- **Adaptive Productivity Intelligence** – Detects overload, adjusts plans, and reduces burnout risk automatically
- **Data-Driven Career Decisions** – Turns career planning into measurable actions rather than guesswork

## 📈 Future Roadmap

### Phase 1: MVP (Hackathon)
- ✅ Resume upload and AI-based analysis
- ✅ Basic career trajectory prediction engine
- ✅ Skill gap detection with recommended roadmap
- ✅ Weekly study planner with simple adaptive logic
- ✅ AWS backend setup for storage and API handling

### Phase 2: Enhanced Intelligence
- 🔄 Advanced career simulation with multiple scenario paths
- 🔄 Improved resume scoring with ATS-style industry matching
- 🔄 Dynamic planner with deadline awareness and exam mode
- 🔄 Burnout risk detection using workload tracking patterns
- 🔄 Integration with job datasets for more realistic predictions

### Phase 3: Scale & Expansion
- 📅 Internship and job recommendation engine
- 📅 Live progress dashboard with analytics and growth charts
- 📅 Multi-role simulation (compare Backend vs ML vs Product paths)
- 📅 Mobile-friendly version with notification system
- 📅 Collaboration features for mentors, peers, and recruiters

## 🤝 Contributing

We welcome contributions that improve FutureForge AI and make career growth more accessible for students and developers.

You can contribute by:
- Reporting bugs
- Suggesting new features
- Improving UI or documentation
- Optimizing AI modules

Fork the repo, make changes, and submit a PR — even small contributions help!

## 📞 Contact

**Shivam Virmani**

- GitHub: [@Shiv-15CC](https://github.com/Shiv-15CC)
- Email: virmanishivam15@gmail.com
- LinkedIn: [Shivam Virmani](https://www.linkedin.com/in/shivam-virmani-9330181b2/)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgements

- **AWS Hackathon organizers for the opportunity and platform**
- **Amazon Web Services for cloud infrastructure and AI tools**
- **Open-source community for libraries, datasets, and inspiration**
- **Developers and mentors who provided guidance during the build process**
- **Friends and testers who helped improve the demo and usability**

