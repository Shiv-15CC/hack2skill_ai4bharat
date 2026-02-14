# FutureForge AI - Technical Design Document

## Document Information
- **Version:** 1.0
- **Date:** February 14, 2026
- **Project:** FutureForge AI - AWS-Powered Career OS
- **Target:** Amazon AI for Bharat Hackathon
- **Architecture:** Serverless, Event-Driven, AI-First

---

## Executive Summary

FutureForge AI is a closed-loop AI Career Operating System built on AWS serverless infrastructure. The system addresses two critical challenges for Indian students: placement anxiety and mental burnout. By integrating career simulation, resume intelligence, and adaptive planning with burnout prevention, FutureForge creates a sustainable growth engine that adapts to user capacity while maximizing career outcomes.

**Core Innovation:** Unlike traditional career tools that operate in isolation, FutureForge implements a feedback-driven architecture where:
- Career goals dictate study plans (forward flow)
- Mental health limits constrain workload (backward pressure)
- Resume improvements trigger trajectory recalculation (lateral integration)
- Progress velocity adjusts future planning (temporal adaptation)

---

## 1. Architecture Overview

### 1.1 Tri-Engine Architecture

FutureForge AI is built on three interconnected AI engines that form a closed-loop system:

#### Engine 1: Career Simulation Engine (Predictor)
**Purpose:** Generate data-driven 5-year career trajectories with probabilistic outcomes.

**Core Capabilities:**
- Multi-scenario projection (best/average/worst case)
- Salary trajectory prediction in LPA (Indian market)
- Skill gap analysis with impact scoring
- Risk assessment (stagnation, burnout probability)
- Feasibility validation (mathematical impossibility detection)

**Requirements Mapping:** REQ-SIM-001 through REQ-SIM-023

#### Engine 2: Resume Intelligence Layer (Analyzer)
**Purpose:** Transform resume from static document to dynamic career optimization tool.

**Core Capabilities:**
- ATS compatibility scoring
- Content quality analysis with quantification detection
- Role alignment with job market data
- Impact-driven feedback (salary impact of changes)
- ROI-based improvement prioritization

**Requirements Mapping:** REQ-RES-001 through REQ-RES-025

#### Engine 3: Adaptive Study & Productivity Planner (Executor)
**Purpose:** Execute career plans with intelligent, dynamic scheduling and burnout prevention.

**Core Capabilities:**
- Personalized roadmap generation
- Real-time plan rebalancing
- Context-aware scheduling
- Burnout detection and Recovery Mode
- Learning velocity tracking

**Requirements Mapping:** REQ-PLAN-001 through REQ-PLAN-023, REQ-BURN-001 through REQ-BURN-023

### 1.2 Integration Philosophy

The three engines operate as a unified system through:

1. **Forward Flow:** Career Simulation → Resume Intelligence → Study Planner
2. **Backward Pressure:** Burnout Engine → Study Planner → Timeline Extension
3. **Lateral Integration:** Resume Updates → Career Re-simulation → Plan Adjustment
4. **Temporal Adaptation:** Progress Tracking → Velocity Calculation → Plan Optimization


---

## 2. High-Level Architecture

### 2.1 AWS Serverless Architecture Diagram

```mermaid
interface CareerSimulationService {
  generateTrajectory(profile: UserProfile): Promise<CareerTrajectoryResult>;

  analyzeSkillGaps(
    currentSkills: Skill[],
    targetRole: string
  ): Promise<SkillGapAnalysis>;

  assessRisks(
    profile: UserProfile,
    plannedHours: number
  ): Promise<RiskAssessment>;

  validateGoalFeasibility(
    goal: CareerGoal,
    learningVelocity: number,
    availableHours: number
  ): Promise<FeasibilityResult>;

  compareScenarios(
    baseProfile: UserProfile,
    scenarios: ActionScenario[]
  ): Promise<ScenarioComparison>;
}

interface UserProfile {
  userId: string;
  resume: ResumeData;
  currentSkills: Skill[];
  gpa?: number;
  experienceLevel: 'fresher' | 'junior' | 'mid' | 'senior';
  learningSpeed: number;
  weeklyAvailableHours: number;
  targetRole: string;
  currentSalary?: number;
  location: string;
}

interface CareerTrajectoryResult {
  scenarios: {
    bestCase: TrajectoryScenario;
    averageCase: TrajectoryScenario;
    worstCase: TrajectoryScenario;
  };
  skillGaps: SkillGapAnalysis;
  risks: RiskAssessment;
  confidence: number;
  generatedAt: Date;
}

interface TrajectoryScenario {
  timeline: YearlyProjection[];
  totalSalaryGrowth: number;
  roleEvolution: RoleTransition[];
  keyMilestones: Milestone[];
  assumptions: string[];
}

interface YearlyProjection {
  year: number;
  salary: {
    amount: number;
    confidenceInterval: { min: number; max: number };
  };
  role: string;
  requiredSkills: string[];
}

interface SkillGapAnalysis {
  missingSkills: SkillGap[];
  totalLearningTime: number;
  dependencyGraph: SkillDependency[];
}

interface SkillGap {
  skillName: string;
  category: 'core' | 'important' | 'nice-to-have';
  impactScore: number;
  estimatedLearningTime: number;
  salaryImpact: number;
  marketDemand: number;
}

interface RiskAssessment {
  stagnationRisk: number;
  burnoutProbability: number;
  careerPivotFeasibility: number;
  warnings: string[];
  recommendations: string[];
}

```
### 2.2 Architecture Principles

**Serverless-First (Cost Efficiency)**
- Zero infrastructure management
- Pay-per-use pricing model
- Automatic scaling from 0 to millions
- Validates: REQ-NFR-007, REQ-NFR-008

**Event-Driven (Loose Coupling)**
- EventBridge for inter-service communication
- Asynchronous processing for heavy workloads
- Decoupled modules for independent deployment
- Validates: REQ-INT-009, REQ-NFR-051

**AI-Native (Intelligence at Core)**
- Amazon Bedrock for generative AI tasks
- SageMaker for custom ML models
- Comprehend for sentiment analysis
- Validates: REQ-SIM-006, REQ-RES-003, REQ-BURN-007

**Data Localization (Compliance)**
- All data stored in AWS Asia Pacific (Mumbai) region
- DynamoDB global tables for disaster recovery
- S3 bucket policies enforce regional constraints
- Validates: REQ-NFR-025, CONST-004

**Security-First (Zero Trust)**
- Cognito for authentication/authorization
- WAF for API protection
- Encryption at rest (S3, DynamoDB) and in transit (TLS 1.3)
- Validates: REQ-NFR-017, REQ-NFR-018, REQ-NFR-019


---

## 3. Component Specifications & Interfaces

### 3.1 Career Simulation Service

**Validates:** REQ-SIM-006, REQ-SIM-007, REQ-SIM-008, REQ-SIM-011, REQ-SIM-016

```typescript
/**
 * Career Simulation Service Interface
 * Generates 5-year career trajectory predictions with risk assessment
 */
interface CareerSimulationService {
  /**
   * Generate career trajectory projections
   * @param profile - User profile with skills, experience, and goals
   * @returns Three scenario projections (best/average/worst)
   * @throws InvalidProfileError if mandatory fields missing
   * @performance Must complete within 10 seconds (REQ-NFR-002)
   */
  generateTrajectory(profile: UserProfile): Promise<CareerTrajectoryResult>;

  /**
   * Analyze skill gaps against target role
   * @param currentSkills - User's current skill set
   * @param targetRole - Desired role (e.g., "Backend Developer")
   * @returns Ranked list of missing skills with impact scores
   * @validates REQ-SIM-011, REQ-SIM-012, REQ-SIM-013
   */
  analyzeSkillGaps(
    currentSkills: Skill[],
    targetRole: string
  ): Promise<SkillGapAnalysis>;

  /**
   * Calculate risk metrics for career trajectory
   * @param profile - User profile
   * @param plannedHours - Weekly study hours
   * @returns Risk scores for stagnation and burnout
   * @validates REQ-SIM-016, REQ-SIM-017, REQ-SIM-018
   */
  assessRisks(
    profile: UserProfile,
    plannedHours: number
  ): Promise<RiskAssessment>;

  /**
   * Validate goal feasibility
   * @param goal - Target salary and timeline
   * @param learningVelocity - User's learning speed
   * @param availableHours - Weekly available hours
   * @returns Feasibility score and adjusted timeline if needed
   * @validates REQ-SIM-019, REQ-NFR-056
   */
  validateGoalFeasibility(
    goal: CareerGoal,
    learningVelocity: number,
    availableHours: number
  ): Promise<FeasibilityResult>;

  /**
   * Compare different action scenarios
   * @param baseProfile - Current user state
   * @param scenarios - Array of potential actions
   * @returns Side-by-side comparison with salary impact
   * @validates REQ-SIM-021, REQ-SIM-022
   */
  compareScenarios(
    baseProfile: UserProfile,
    scenarios: ActionScenario[]
  ): Promise<ScenarioComparison>;
}

/**
 * User Profile Data Model
 */
interface UserProfile {
  userId: string;
  resume: ResumeData;
  currentSkills: Skill[];
  gpa?: number;
  experienceLevel: 'fresher' | 'junior' | 'mid' | 'senior';
  learningSpeed: number; // 1-10 scale
  weeklyAvailableHours: number; // 0-168
  targetRole: string;
  currentSalary?: number; // in LPA
  location: string; // for market data
}

/**
 * Career Trajectory Result
 */
interface CareerTrajectoryResult {
  scenarios: {
    bestCase: TrajectoryScenario;
    averageCase: TrajectoryScenario;
    worstCase: TrajectoryScenario;
  };
  skillGaps: SkillGapAnalysis;
  risks: RiskAssessment;
  confidence: number; // 0-100%
  generatedAt: Date;
}

/**
 * Trajectory Scenario
 */
interface TrajectoryScenario {
  timeline: YearlyProjection[];
  totalSalaryGrowth: number; // in LPA
  roleEvolution: RoleTransition[];
  keyMilestones: Milestone[];
  assumptions: string[];
}

/**
 * Yearly Projection
 */
interface YearlyProjection {
  year: number;
  salary: {
    amount: number; // in LPA
    confidenceInterval: { min: number; max: number };
  };
  role: string;
  requiredSkills: string[];
}

/**
 * Skill Gap Analysis
 */
interface SkillGapAnalysis {
  missingSkills: SkillGap[];
  totalLearningTime: number; // in hours
  dependencyGraph: SkillDependency[];
}

/**
 * Skill Gap with Impact
 */
interface SkillGap {
  skillName: string;
  category: 'core' | 'important' | 'nice-to-have';
  impactScore: number; // 1-100
  estimatedLearningTime: number; // in hours
  salaryImpact: number; // in LPA
  marketDemand: number; // 0-100%
}

/**
 * Risk Assessment
 */
interface RiskAssessment {
  stagnationRisk: number; // 0-100%
  burnoutProbability: number; // 0-100%
  careerPivotFeasibility: number; // 0-100%
  warnings: string[];
  recommendations: string[];
}
```

### 3.2 Resume Intelligence Service

**Validates:** REQ-RES-002, REQ-RES-003, REQ-RES-004, REQ-RES-009, REQ-RES-018

```typescript
/**
 * Resume Intelligence Service Interface
 * Analyzes resumes and provides actionable optimization feedback
 */
interface ResumeIntelligenceService {
  /**
   * Parse and analyze uploaded resume
   * @param resumeFile - Resume file (PDF/DOCX/TXT, max 5MB)
   * @returns Comprehensive resume analysis with scores
   * @performance Must complete within 5 seconds (REQ-RES-002, REQ-NFR-001)
   * @validates REQ-RES-001, REQ-RES-003, REQ-RES-004
   */
  analyzeResume(resumeFile: File): Promise<ResumeAnalysisResult>;

  /**
   * Check ATS compatibility
   * @param resumeContent - Parsed resume content
   * @returns ATS compatibility score and issues
   * @validates REQ-RES-005, REQ-RES-006, REQ-RES-007, REQ-RES-008
   */
  checkATSCompatibility(resumeContent: ResumeData): Promise<ATSCompatibility>;

  /**
   * Analyze content quality of resume bullets
   * @param bullets - Array of resume bullet points
   * @returns Scored bullets with improvement suggestions
   * @validates REQ-RES-009, REQ-RES-010, REQ-RES-011, REQ-RES-012
   */
  analyzeContentQuality(bullets: string[]): Promise<ContentQualityAnalysis>;

  /**
   * Calculate role alignment score
   * @param resumeContent - Parsed resume
   * @param targetRole - Desired role
   * @returns Alignment score and missing keywords
   * @validates REQ-RES-014, REQ-RES-015, REQ-RES-016, REQ-RES-017
   */
  calculateRoleAlignment(
    resumeContent: ResumeData,
    targetRole: string
  ): Promise<RoleAlignment>;

  /**
   * Generate impact-driven feedback
   * @param resumeScore - Current resume analysis
   * @param careerTrajectory - User's career projection
   * @returns Feedback explaining salary impact of improvements
   * @validates REQ-RES-018, REQ-RES-019, REQ-RES-020, REQ-RES-021
   */
  generateImpactFeedback(
    resumeScore: ResumeScore,
    careerTrajectory: CareerTrajectoryResult
  ): Promise<ImpactFeedback>;

  /**
   * Suggest resume optimizations
   * @param analysis - Resume analysis result
   * @param skillGaps - Identified skill gaps
   * @returns Prioritized improvement roadmap
   * @validates REQ-RES-022, REQ-RES-023, REQ-RES-024, REQ-RES-025
   */
  suggestOptimizations(
    analysis: ResumeAnalysisResult,
    skillGaps: SkillGapAnalysis
  ): Promise<OptimizationRoadmap>;
}

/**
 * Resume Analysis Result
 */
interface ResumeAnalysisResult {
  overallScore: number; // 0-100
  scoreBreakdown: {
    atsCompatibility: number; // 30% weight
    contentQuality: number; // 40% weight
    roleAlignment: number; // 30% weight
  };
  parsedData: ResumeData;
  atsAnalysis: ATSCompatibility;
  contentAnalysis: ContentQualityAnalysis;
  roleAlignment: RoleAlignment;
  processingTime: number; // in milliseconds
  analyzedAt: Date;
}

/**
 * Resume Data Model
 */
interface ResumeData {
  contactInfo: {
    name: string;
    email: string;
    phone?: string;
    linkedin?: string;
    github?: string;
  };
  education: Education[];
  skills: Skill[];
  experience: Experience[];
  projects: Project[];
  certifications: Certification[];
  achievements: string[];
}

/**
 * ATS Compatibility Analysis
 */
interface ATSCompatibility {
  score: number; // 0-100
  issues: ATSIssue[];
  keywordDensity: number; // percentage
  formatWarnings: string[];
  recommendations: string[];
}

/**
 * Content Quality Analysis
 */
interface ContentQualityAnalysis {
  bulletScores: BulletScore[];
  averageScore: number;
  weakBullets: BulletScore[];
  quantificationRate: number; // percentage with metrics
  actionVerbUsage: number; // percentage with strong verbs
}

/**
 * Bullet Score
 */
interface BulletScore {
  text: string;
  score: number; // 0-100
  hasActionVerb: boolean;
  hasQuantification: boolean;
  hasImpact: boolean;
  suggestions: string[];
  improvedVersion?: string;
}

/**
 * Role Alignment
 */
interface RoleAlignment {
  score: number; // 0-100%
  missingKeywords: string[];
  relevanceScores: {
    experience: number;
    projects: number;
    skills: number;
  };
  industryTerminologyGaps: string[];
}

/**
 * Impact Feedback
 */
interface ImpactFeedback {
  currentTrajectory: string; // e.g., "6 LPA path"
  improvements: ImpactImprovement[];
  prioritizedActions: PrioritizedAction[];
  totalPotentialGain: number; // in LPA
}

/**
 * Impact Improvement
 */
interface ImpactImprovement {
  issue: string;
  impact: string; // e.g., "-32% backend role probability"
  salaryImpact: number; // in LPA
  effortRequired: 'low' | 'medium' | 'high';
  roi: number; // salary gain per effort unit
}
```


### 3.3 Adaptive Planner Service

**Validates:** REQ-PLAN-001, REQ-PLAN-012, REQ-PLAN-013, REQ-PLAN-014, REQ-PLAN-015

```typescript
/**
 * Adaptive Planner Service Interface
 * Generates and dynamically adjusts personalized study roadmaps
 */
interface AdaptivePlannerService {
  /**
   * Generate initial study roadmap
   * @param skillGaps - Identified skill gaps from simulation
   * @param userProfile - User constraints and preferences
   * @returns Personalized study plan with milestones
   * @validates REQ-PLAN-001, REQ-PLAN-002, REQ-PLAN-003, REQ-PLAN-004, REQ-PLAN-005
   */
  generateRoadmap(
    skillGaps: SkillGapAnalysis,
    userProfile: UserProfile
  ): Promise<StudyPlan>;

  /**
   * Rebalance plan when tasks are skipped or delayed
   * @param currentPlan - Active study plan
   * @param missedTasks - Tasks not completed
   * @returns Updated plan with rebalanced schedule
   * @performance Must complete within 1 minute (REQ-PLAN-013, REQ-NFR-006)
   * @validates REQ-PLAN-012, REQ-PLAN-013, REQ-PLAN-014
   */
  rebalancePlan(
    currentPlan: StudyPlan,
    missedTasks: Task[]
  ): Promise<StudyPlan>;

  /**
   * Adjust plan based on user context
   * @param plan - Current study plan
   * @param context - User context (exam, interview, low energy)
   * @returns Context-adjusted plan
   * @validates REQ-PLAN-014, REQ-PLAN-017, REQ-PLAN-019
   */
  adjustForContext(
    plan: StudyPlan,
    context: UserContext
  ): Promise<StudyPlan>;

  /**
   * Adapt plan based on completion rate
   * @param plan - Current study plan
   * @param completionRate - Percentage of tasks completed
   * @returns Intensity-adjusted plan
   * @validates REQ-PLAN-015, REQ-PLAN-016
   */
  adaptToPerformance(
    plan: StudyPlan,
    completionRate: number
  ): Promise<StudyPlan>;

  /**
   * Recommend learning resources
   * @param skill - Skill to learn
   * @param userLevel - User's current proficiency
   * @returns Curated resource list
   * @validates REQ-PLAN-007, REQ-PLAN-008, REQ-PLAN-009, REQ-PLAN-010
   */
  recommendResources(
    skill: string,
    userLevel: number
  ): Promise<ResourceRecommendation[]>;

  /**
   * Generate internship preparation timeline
   * @param targetDate - Interview/application date
   * @param currentSkills - User's current skills
   * @returns Focused prep plan
   * @validates REQ-PLAN-021, REQ-PLAN-022, REQ-PLAN-023
   */
  generateInternshipPrep(
    targetDate: Date,
    currentSkills: Skill[]
  ): Promise<InternshipPrepPlan>;
}

/**
 * Study Plan Data Model
 */
interface StudyPlan {
  planId: string;
  userId: string;
  version: number;
  createdAt: Date;
  updatedAt: Date;
  
  timeline: {
    startDate: Date;
    endDate: Date;
    totalWeeks: number;
  };
  
  weeklySchedule: WeeklySchedule[];
  milestones: Milestone[];
  
  constraints: {
    weeklyHours: number;
    mandatoryRestDays: number[];
    blockedTimeSlots?: TimeSlot[];
  };
  
  metadata: {
    totalTasks: number;
    completedTasks: number;
    completionRate: number;
    currentWeek: number;
  };
}

/**
 * Weekly Schedule
 */
interface WeeklySchedule {
  weekNumber: number;
  startDate: Date;
  endDate: Date;
  totalHours: number;
  dailyTasks: DailyTask[];
  weeklyGoal: string;
  status: 'upcoming' | 'active' | 'completed' | 'rebalanced';
}

/**
 * Daily Task
 */
interface DailyTask {
  taskId: string;
  date: Date;
  skill: string;
  title: string;
  description: string;
  estimatedTime: number; // in minutes
  difficulty: 'easy' | 'medium' | 'hard';
  type: 'reading' | 'video' | 'coding' | 'practice' | 'project' | 'review';
  resources: ResourceRecommendation[];
  dependencies: string[]; // taskIds
  status: 'pending' | 'in-progress' | 'completed' | 'skipped';
  completedAt?: Date;
}

/**
 * Task (Generic)
 */
interface Task {
  taskId: string;
  title: string;
  skill: string;
  estimatedTime: number;
  difficulty: 'easy' | 'medium' | 'hard';
  status: 'pending' | 'in-progress' | 'completed' | 'skipped';
}

/**
 * User Context
 */
interface UserContext {
  type: 'exam' | 'interview' | 'low-energy' | 'high-motivation' | 'normal';
  date?: Date;
  description?: string;
  priority: 'high' | 'medium' | 'low';
}

/**
 * Resource Recommendation
 */
interface ResourceRecommendation {
  title: string;
  type: 'course' | 'tutorial' | 'documentation' | 'practice' | 'project';
  platform: string;
  url: string;
  duration: number; // in hours
  difficulty: 'beginner' | 'intermediate' | 'advanced';
  cost: number; // 0 for free
  rating?: number;
  relevanceScore: number; // 0-100
}

/**
 * Milestone
 */
interface Milestone {
  milestoneId: string;
  name: string;
  description: string;
  targetDate: Date;
  type: 'resume-ready' | 'interview-ready' | 'internship-ready' | 'job-ready' | 'custom';
  requirements: string[];
  status: 'pending' | 'in-progress' | 'completed';
  completedAt?: Date;
}

/**
 * Internship Prep Plan
 */
interface InternshipPrepPlan extends StudyPlan {
  targetCompanies: string[];
  focusAreas: string[];
  mockInterviews: MockInterviewSchedule[];
  companyResearch: CompanyResearchTask[];
}
```

### 3.4 Burnout Monitor Service

**Validates:** REQ-BURN-001, REQ-BURN-003, REQ-BURN-009, REQ-BURN-011, REQ-BURN-016

```typescript
/**
 * Burnout Monitor Service Interface
 * Tracks workload, mood, and triggers Recovery Mode when needed
 */
interface BurnoutMonitorService {
  /**
   * Calculate real-time burnout risk
   * @param userId - User identifier
   * @returns Burnout risk score and contributing factors
   * @validates REQ-BURN-016, REQ-BURN-017
   */
  calculateBurnoutRisk(userId: string): Promise<BurnoutRiskIndex>;

  /**
   * Track daily study hours
   * @param userId - User identifier
   * @param hours - Hours studied today
   * @validates REQ-BURN-001, REQ-BURN-002, REQ-BURN-003
   */
  trackStudyHours(userId: string, hours: number): Promise<void>;

  /**
   * Process mood log entry
   * @param userId - User identifier
   * @param moodEntry - Mood rating and optional notes
   * @returns Sentiment analysis result
   * @validates REQ-BURN-006, REQ-BURN-007, REQ-BURN-008, REQ-BURN-009
   */
  processMoodLog(
    userId: string,
    moodEntry: MoodEntry
  ): Promise<SentimentAnalysis>;

  /**
   * Check if Recovery Mode should activate
   * @param userId - User identifier
   * @returns Recovery Mode status and reason
   * @validates REQ-BURN-009, REQ-BURN-011
   */
  checkRecoveryMode(userId: string): Promise<RecoveryModeStatus>;

  /**
   * Activate Recovery Mode
   * @param userId - User identifier
   * @param reason - Trigger reason
   * @returns Updated study plan with reduced intensity
   * @validates REQ-BURN-011, REQ-BURN-012, REQ-BURN-013, REQ-BURN-015
   */
  activateRecoveryMode(
    userId: string,
    reason: string
  ): Promise<RecoveryModePlan>;

  /**
   * Exit Recovery Mode
   * @param userId - User identifier
   * @returns Restored study plan
   * @validates REQ-BURN-014
   */
  exitRecoveryMode(userId: string): Promise<StudyPlan>;

  /**
   * Monitor wellness metrics
   * @param userId - User identifier
   * @param metrics - Sleep, exercise data
   * @validates REQ-BURN-021, REQ-BURN-022
   */
  trackWellness(
    userId: string,
    metrics: WellnessMetrics
  ): Promise<void>;
}

/**
 * Burnout Risk Index
 */
interface BurnoutRiskIndex {
  overallRisk: number; // 0-100%
  riskLevel: 'low' | 'moderate' | 'high' | 'critical';
  
  factors: {
    studyHours: {
      current7DayAvg: number;
      sustainableThreshold: number;
      contribution: number; // 0-100%
    };
    consistency: {
      completionRate: number;
      recentDrop: number; // percentage drop
      contribution: number;
    };
    mood: {
      averageRating: number; // 1-5
      trend: 'improving' | 'stable' | 'declining';
      contribution: number;
    };
    taskCompletion: {
      rate: number; // percentage
      contribution: number;
    };
  };
  
  warnings: string[];
  recommendations: string[];
  shouldTriggerRecovery: boolean;
  calculatedAt: Date;
}

/**
 * Mood Entry
 */
interface MoodEntry {
  userId: string;
  date: Date;
  rating: 1 | 2 | 3 | 4 | 5;
  notes?: string;
  tags?: string[]; // e.g., 'stressed', 'motivated', 'tired'
}

/**
 * Sentiment Analysis
 */
interface SentimentAnalysis {
  sentiment: 'positive' | 'neutral' | 'negative';
  confidence: number; // 0-100%
  emotions: {
    frustration: number;
    overwhelm: number;
    motivation: number;
    satisfaction: number;
  };
  stressIndicators: string[];
  actionRequired: boolean;
}

/**
 * Recovery Mode Status
 */
interface RecoveryModeStatus {
  isActive: boolean;
  activatedAt?: Date;
  reason?: string;
  daysRemaining?: number;
  shouldActivate: boolean;
  triggers: string[];
}

/**
 * Recovery Mode Plan
 */
interface RecoveryModePlan {
  planId: string;
  userId: string;
  activatedAt: Date;
  duration: number; // days
  
  reducedSchedule: WeeklySchedule[];
  allowedTaskTypes: ('reading' | 'video' | 'review')[];
  maxDailyHours: number;
  
  motivationalMessages: string[];
  progressHighlights: string[];
  
  exitCriteria: {
    consecutiveDaysAboveThreshold: number;
    minimumMoodRating: number;
  };
}

/**
 * Wellness Metrics
 */
interface WellnessMetrics {
  date: Date;
  sleepHours?: number;
  exerciseMinutes?: number;
  stressLevel?: 1 | 2 | 3 | 4 | 5;
}
```


---

## 4. Data Models

### 4.1 DynamoDB Table Design

**Table Strategy:** Single-table design with GSIs for access patterns

#### Primary Table: `FutureForge-Main`

**Partition Key:** `PK` (String)  
**Sort Key:** `SK` (String)

**Access Patterns:**

| Pattern | PK | SK | GSI |
|---------|----|----|-----|
| User Profile | USER#\{userId\} | PROFILE | - |
| Career Trajectory | USER#\{userId\} | TRAJECTORY#\{timestamp\} | - |
| Resume Analysis | USER#\{userId\} | RESUME#\{version\} | - |
| Study Plan | USER#\{userId\} | PLAN#\{planId\} | - |
| Daily Tasks | USER#\{userId\} | TASK#\{date\}#\{taskId\} | GSI1 (by date) |
| Mood Logs | USER#\{userId\} | MOOD#\{date\} | - |
| Burnout Risk | USER#\{userId\} | BURNOUT#CURRENT | - |
| Progress Metrics | USER#\{userId\} | PROGRESS#\{date\} | - |

### 4.2 TypeScript Data Models

```typescript
/**
 * Career Trajectory Model
 * Stored in DynamoDB and cached in ElastiCache
 */
interface CareerTrajectory {
  PK: string; // USER#{userId}
  SK: string; // TRAJECTORY#{timestamp}
  
  userId: string;
  version: number;
  createdAt: string; // ISO 8601
  
  scenarios: {
    bestCase: TrajectoryScenario;
    averageCase: TrajectoryScenario;
    worstCase: TrajectoryScenario;
  };
  
  skillGaps: SkillGapAnalysis;
  risks: RiskAssessment;
  confidence: number;
  
  inputProfile: {
    skills: string[];
    experience: string;
    targetRole: string;
    weeklyHours: number;
  };
  
  TTL?: number; // Auto-expire old trajectories after 90 days
}

/**
 * Resume Score Model
 * Stored in DynamoDB with S3 reference for full resume
 */
interface ResumeScore {
  PK: string; // USER#{userId}
  SK: string; // RESUME#{version}
  
  userId: string;
  version: number;
  analyzedAt: string;
  
  s3Key: string; // Reference to resume file in S3
  
  overallScore: number;
  scoreBreakdown: {
    atsCompatibility: number;
    contentQuality: number;
    roleAlignment: number;
  };
  
  atsAnalysis: {
    score: number;
    issues: string[];
    keywordDensity: number;
  };
  
  contentAnalysis: {
    averageScore: number;
    quantificationRate: number;
    weakBullets: number;
  };
  
  roleAlignment: {
    score: number;
    missingKeywords: string[];
  };
  
  impactFeedback: {
    currentTrajectory: string;
    improvements: Array<{
      issue: string;
      impact: string;
      salaryImpact: number;
      roi: number;
    }>;
  };
  
  processingTime: number; // milliseconds
}

/**
 * Study Plan Model
 * Stored in DynamoDB with frequent updates
 */
interface StudyPlanModel {
  PK: string; // USER#{userId}
  SK: string; // PLAN#{planId}
  
  planId: string;
  userId: string;
  version: number;
  status: 'active' | 'completed' | 'archived';
  
  createdAt: string;
  updatedAt: string;
  
  timeline: {
    startDate: string;
    endDate: string;
    totalWeeks: number;
  };
  
  constraints: {
    weeklyHours: number;
    mandatoryRestDays: number[];
  };
  
  metadata: {
    totalTasks: number;
    completedTasks: number;
    completionRate: number;
    currentWeek: number;
  };
  
  // Weekly schedules stored as separate items for efficient updates
  weeklyScheduleRefs: string[]; // SK references
}

/**
 * Burnout Risk Index Model
 * Updated frequently, stored with current snapshot
 */
interface BurnoutRiskModel {
  PK: string; // USER#{userId}
  SK: string; // BURNOUT#CURRENT
  
  userId: string;
  calculatedAt: string;
  
  overallRisk: number;
  riskLevel: 'low' | 'moderate' | 'high' | 'critical';
  
  factors: {
    studyHours: {
      current7DayAvg: number;
      sustainableThreshold: number;
      contribution: number;
    };
    consistency: {
      completionRate: number;
      recentDrop: number;
      contribution: number;
    };
    mood: {
      averageRating: number;
      trend: 'improving' | 'stable' | 'declining';
      contribution: number;
    };
    taskCompletion: {
      rate: number;
      contribution: number;
    };
  };
  
  warnings: string[];
  recommendations: string[];
  shouldTriggerRecovery: boolean;
  
  recoveryMode?: {
    isActive: boolean;
    activatedAt?: string;
    reason?: string;
    daysRemaining?: number;
  };
  
  // Historical data stored separately for trend analysis
  historicalRiskScores: Array<{
    date: string;
    risk: number;
  }>;
}

/**
 * Progress Metrics Model
 * Daily snapshots for velocity tracking
 */
interface ProgressMetrics {
  PK: string; // USER#{userId}
  SK: string; // PROGRESS#{date}
  
  userId: string;
  date: string;
  
  learningVelocity: number;
  
  skillProficiency: Record<string, {
    level: number; // 0-100%
    category: 'beginner' | 'intermediate' | 'advanced' | 'expert';
    lastUpdated: string;
  }>;
  
  streaks: {
    currentStreak: number;
    longestStreak: number;
    lastActiveDate: string;
  };
  
  achievements: Array<{
    achievementId: string;
    name: string;
    unlockedAt: string;
  }>;
  
  weeklyStats: {
    tasksCompleted: number;
    hoursInvested: number;
    skillsImproved: string[];
  };
}
```

### 4.3 S3 Bucket Structure

```
futureforge-resumes-{region}/
├── {userId}/
│   ├── original/
│   │   └── resume-v{version}.pdf
│   ├── parsed/
│   │   └── resume-v{version}.json
│   └── analysis/
│       └── analysis-v{version}.json

futureforge-market-data-{region}/
├── job-descriptions/
│   └── {role}/
│       └── {timestamp}.json
├── salary-benchmarks/
│   └── {year}/
│       └── {role}-{location}.json
└── skill-taxonomies/
    └── taxonomy-v{version}.json
```

### 4.4 OpenSearch Index Schema

```json
{
  "job_market_index": {
    "mappings": {
      "properties": {
        "role": { "type": "keyword" },
        "company": { "type": "text" },
        "location": { "type": "keyword" },
        "salary_min": { "type": "integer" },
        "salary_max": { "type": "integer" },
        "required_skills": { "type": "keyword" },
        "description": { "type": "text" },
        "posted_date": { "type": "date" },
        "experience_level": { "type": "keyword" },
        "embedding": {
          "type": "knn_vector",
          "dimension": 1536
        }
      }
    }
  }
}
```


---

## 5. Core Correctness Properties

### 5.1 Trajectory Accuracy Property

**Property 1: Salary Projection Consistency**

The system shall ensure salary projections are monotonically increasing or stable (never decreasing without explicit career pivot) and bounded by market data.

**Formal Specification:**
```
∀ year_i, year_j in trajectory where i < j:
  salary(year_j) >= salary(year_i) * 0.95  // Allow 5% variance
  AND salary(year_j) <= market_max(role, location) * 1.2  // 20% optimism buffer
```

**Validates:** REQ-SIM-007, REQ-SIM-010, REQ-NFR-058

**Implementation:**
- Post-processing validation in `CareerSimulationService.generateTrajectory()`
- Cross-reference with DynamoDB market data
- Flag outliers for manual review

**Test Strategy:**
```typescript
// Property-based test
test('salary trajectory is monotonically increasing', () => {
  fc.assert(
    fc.property(
      fc.record({
        skills: fc.array(fc.string()),
        experience: fc.integer(0, 10),
        targetRole: fc.constantFrom('Backend', 'Frontend', 'ML Engineer')
      }),
      async (profile) => {
        const trajectory = await simulationService.generateTrajectory(profile);
        
        for (let i = 0; i < trajectory.scenarios.averageCase.timeline.length - 1; i++) {
          const current = trajectory.scenarios.averageCase.timeline[i].salary.amount;
          const next = trajectory.scenarios.averageCase.timeline[i + 1].salary.amount;
          
          expect(next).toBeGreaterThanOrEqual(current * 0.95);
        }
      }
    )
  );
});
```

---

### 5.2 Performance Guarantee Property

**Property 2: Resume Analysis Latency**

The system shall return resume analysis results within 5 seconds for 95th percentile of requests.

**Formal Specification:**
```
P(analysis_time <= 5000ms) >= 0.95
```

**Validates:** REQ-RES-002, REQ-NFR-001

**Implementation:**
- Lambda timeout set to 10 seconds (2× SLA)
- CloudWatch alarm if p95 latency > 5s
- Automatic fallback to cached analysis if Bedrock is slow

**Monitoring:**
```typescript
// CloudWatch metric
const metric = new cloudwatch.Metric({
  namespace: 'FutureForge',
  metricName: 'ResumeAnalysisLatency',
  statistic: 'p95',
  period: Duration.minutes(5)
});

// Alarm
new cloudwatch.Alarm(this, 'ResumeAnalysisLatencyAlarm', {
  metric: metric,
  threshold: 5000,
  evaluationPeriods: 2,
  alarmDescription: 'Resume analysis p95 latency exceeds 5s'
});
```

---

### 5.3 Plan Rebalancing Correctness Property

**Property 3: Hour Constraint Preservation**

When rebalancing study plans, the system shall never exceed user's weekly available hours by more than 10%.

**Formal Specification:**
```
∀ week in rebalanced_plan:
  sum(task.estimatedTime for task in week.dailyTasks) <= user.weeklyAvailableHours * 1.1
```

**Validates:** REQ-PLAN-005, REQ-PLAN-006, REQ-PLAN-013

**Implementation:**
```typescript
async function rebalancePlan(
  currentPlan: StudyPlan,
  missedTasks: Task[]
): Promise<StudyPlan> {
  const rebalanced = await performRebalancing(currentPlan, missedTasks);
  
  // Correctness check
  for (const week of rebalanced.weeklySchedule) {
    const totalHours = week.dailyTasks.reduce(
      (sum, task) => sum + task.estimatedTime / 60,
      0
    );
    
    const maxAllowed = currentPlan.constraints.weeklyHours * 1.1;
    
    if (totalHours > maxAllowed) {
      // Violation detected - extend timeline instead
      return extendTimeline(rebalanced, totalHours - maxAllowed);
    }
  }
  
  return rebalanced;
}
```

**Test Strategy:**
```typescript
// Property-based test
test('rebalanced plan respects hour constraints', () => {
  fc.assert(
    fc.property(
      fc.record({
        weeklyHours: fc.integer(10, 60),
        missedTasks: fc.array(fc.record({
          estimatedTime: fc.integer(30, 240) // minutes
        }), { minLength: 1, maxLength: 10 })
      }),
      async ({ weeklyHours, missedTasks }) => {
        const plan = await createTestPlan(weeklyHours);
        const rebalanced = await plannerService.rebalancePlan(plan, missedTasks);
        
        for (const week of rebalanced.weeklySchedule) {
          const totalMinutes = week.dailyTasks.reduce(
            (sum, task) => sum + task.estimatedTime,
            0
          );
          const totalHours = totalMinutes / 60;
          
          expect(totalHours).toBeLessThanOrEqual(weeklyHours * 1.1);
        }
      }
    )
  );
});
```

---

### 5.4 Burnout Detection Property

**Property 4: Recovery Mode Activation**

The system shall activate Recovery Mode when mood rating is below 2 for 3+ consecutive days OR burnout risk exceeds 70%.

**Formal Specification:**
```
recovery_mode_active = 
  (consecutive_days(mood < 2) >= 3) OR (burnout_risk > 70)
```

**Validates:** REQ-BURN-009, REQ-BURN-011, REQ-BURN-017

**Implementation:**
```typescript
async function checkRecoveryMode(userId: string): Promise<RecoveryModeStatus> {
  const moodLogs = await getMoodLogs(userId, 7); // Last 7 days
  const burnoutRisk = await calculateBurnoutRisk(userId);
  
  // Check consecutive low mood days
  let consecutiveLowMood = 0;
  for (let i = moodLogs.length - 1; i >= 0; i--) {
    if (moodLogs[i].rating < 2) {
      consecutiveLowMood++;
    } else {
      break;
    }
  }
  
  const shouldActivate = 
    consecutiveLowMood >= 3 || 
    burnoutRisk.overallRisk > 70;
  
  return {
    isActive: false, // Will be set by activateRecoveryMode
    shouldActivate,
    triggers: [
      ...(consecutiveLowMood >= 3 ? [`Low mood for ${consecutiveLowMood} days`] : []),
      ...(burnoutRisk.overallRisk > 70 ? [`Burnout risk: ${burnoutRisk.overallRisk}%`] : [])
    ]
  };
}
```

**Test Strategy:**
```typescript
// Property-based test for Recovery Mode trigger
test('recovery mode activates on correct conditions', () => {
  fc.assert(
    fc.property(
      fc.record({
        moodSequence: fc.array(fc.integer(1, 5), { minLength: 7, maxLength: 7 }),
        burnoutRisk: fc.integer(0, 100)
      }),
      async ({ moodSequence, burnoutRisk }) => {
        // Setup test data
        const userId = 'test-user';
        await setupMoodLogs(userId, moodSequence);
        await setupBurnoutRisk(userId, burnoutRisk);
        
        const status = await burnoutService.checkRecoveryMode(userId);
        
        // Calculate expected result
        let consecutiveLow = 0;
        for (let i = moodSequence.length - 1; i >= 0; i--) {
          if (moodSequence[i] < 2) consecutiveLow++;
          else break;
        }
        
        const expectedActivation = consecutiveLow >= 3 || burnoutRisk > 70;
        
        expect(status.shouldActivate).toBe(expectedActivation);
      }
    )
  );
});
```

---

### 5.5 Goal Feasibility Property

**Property 5: Mathematical Impossibility Detection**

The system shall warn users when target goals are mathematically impossible given learning velocity and available hours.

**Formal Specification:**
```
required_hours = sum(skill.learningTime for skill in skill_gaps)
available_hours = user.weeklyHours * weeks_until_deadline

IF required_hours > available_hours * user.learningVelocity:
  THEN warn_impossible = true
```

**Validates:** REQ-SIM-019, REQ-NFR-056

**Implementation:**
```typescript
async function validateGoalFeasibility(
  goal: CareerGoal,
  learningVelocity: number,
  availableHours: number
): Promise<FeasibilityResult> {
  const skillGaps = await analyzeSkillGaps(goal.requiredSkills, goal.currentSkills);
  
  const requiredHours = skillGaps.missingSkills.reduce(
    (sum, skill) => sum + skill.estimatedLearningTime,
    0
  );
  
  const weeksAvailable = differenceInWeeks(goal.targetDate, new Date());
  const totalAvailableHours = availableHours * weeksAvailable;
  
  // Adjust for learning velocity (1.0 = average, 2.0 = 2x faster)
  const effectiveHours = totalAvailableHours * learningVelocity;
  
  const feasibilityScore = Math.min(100, (effectiveHours / requiredHours) * 100);
  
  if (feasibilityScore < 80) {
    const adjustedWeeks = Math.ceil(requiredHours / (availableHours * learningVelocity));
    const adjustedDate = addWeeks(new Date(), adjustedWeeks);
    
    return {
      isFeasible: false,
      feasibilityScore,
      warning: `Goal requires ${requiredHours}h but only ${effectiveHours}h available`,
      adjustedTimeline: {
        originalDate: goal.targetDate,
        adjustedDate,
        additionalWeeks: adjustedWeeks - weeksAvailable
      }
    };
  }
  
  return {
    isFeasible: true,
    feasibilityScore
  };
}
```

---

### 5.6 Data Integrity Property

**Property 6: Cross-Module Consistency**

When resume is updated, career trajectory must be recalculated within 5 seconds to maintain consistency.

**Formal Specification:**
```
∀ resume_update_event:
  timestamp(trajectory_recalculation) - timestamp(resume_update) <= 5000ms
```

**Validates:** REQ-INT-004, REQ-INT-009

**Implementation:**
```typescript
// EventBridge rule
const resumeUpdateRule = new events.Rule(this, 'ResumeUpdateRule', {
  eventPattern: {
    source: ['futureforge.resume'],
    detailType: ['Resume Updated']
  }
});

// Target: Step Functions workflow
resumeUpdateRule.addTarget(new targets.SfnStateMachine(trajectoryRecalcWorkflow));

// Step Functions workflow
const trajectoryRecalcWorkflow = new sfn.StateMachine(this, 'TrajectoryRecalc', {
  definition: sfn.Chain
    .start(new tasks.LambdaInvoke(this, 'GetUpdatedResume', {
      lambdaFunction: getResumeFunction
    }))
    .next(new tasks.LambdaInvoke(this, 'RecalculateTrajectory', {
      lambdaFunction: simulationFunction,
      timeout: Duration.seconds(10)
    }))
    .next(new tasks.DynamoPutItem(this, 'SaveTrajectory', {
      table: mainTable,
      item: {
        PK: sfn.DynamoAttributeValue.fromString(sfn.JsonPath.stringAt('$.userId')),
        SK: sfn.DynamoAttributeValue.fromString('TRAJECTORY#CURRENT')
      }
    })),
  timeout: Duration.seconds(15)
});
```


---

## 6. Error Handling & Recovery

### 6.1 ML Model Failures

#### 6.1.1 Bedrock API Failures

**Scenario:** Amazon Bedrock API timeout or rate limit exceeded

**Detection:**
```typescript
try {
  const response = await bedrockClient.invokeModel({
    modelId: 'anthropic.claude-3-sonnet',
    body: JSON.stringify(prompt)
  });
} catch (error) {
  if (error.name === 'ThrottlingException') {
    // Rate limit exceeded
  } else if (error.name === 'TimeoutError') {
    // API timeout
  }
}
```

**Recovery Strategy:**
1. **Exponential Backoff:** Retry with exponential backoff (1s, 2s, 4s)
2. **Fallback to Cache:** Return cached analysis if available (< 24 hours old)
3. **Degraded Mode:** Use rule-based analysis instead of AI
4. **User Notification:** Inform user of degraded service

**Validates:** REQ-NFR-015

```typescript
async function analyzeResumeWithFallback(
  resumeFile: File
): Promise<ResumeAnalysisResult> {
  try {
    return await analyzeWithBedrock(resumeFile);
  } catch (error) {
    logger.warn('Bedrock analysis failed, attempting fallback', { error });
    
    // Try cache
    const cached = await getCachedAnalysis(resumeFile.hash);
    if (cached && isRecent(cached, 24 * 60 * 60 * 1000)) {
      return cached;
    }
    
    // Fallback to rule-based
    logger.info('Using rule-based analysis fallback');
    return await analyzeWithRules(resumeFile);
  }
}
```

#### 6.1.2 ML Hallucination Detection

**Scenario:** AI generates unrealistic salary projections or invalid recommendations

**Detection:**
```typescript
function validateTrajectory(trajectory: CareerTrajectoryResult): ValidationResult {
  const errors: string[] = [];
  
  // Check salary bounds
  for (const year of trajectory.scenarios.averageCase.timeline) {
    if (year.salary.amount < 0 || year.salary.amount > 200) {
      errors.push(`Invalid salary: ${year.salary.amount} LPA in year ${year.year}`);
    }
  }
  
  // Check monotonicity
  for (let i = 0; i < trajectory.scenarios.averageCase.timeline.length - 1; i++) {
    const current = trajectory.scenarios.averageCase.timeline[i].salary.amount;
    const next = trajectory.scenarios.averageCase.timeline[i + 1].salary.amount;
    
    if (next < current * 0.8) {
      errors.push(`Unrealistic salary drop: ${current} to ${next} LPA`);
    }
  }
  
  // Check skill gaps are non-empty
  if (trajectory.skillGaps.missingSkills.length === 0 && 
      trajectory.scenarios.bestCase.timeline[0].salary.amount < 50) {
    errors.push('No skill gaps identified for low starting salary');
  }
  
  return {
    isValid: errors.length === 0,
    errors
  };
}
```

**Recovery Strategy:**
1. **Validation Layer:** Post-process all AI outputs through validation
2. **Constraint Enforcement:** Apply hard constraints (salary bounds, skill requirements)
3. **Regeneration:** Retry with modified prompt if validation fails
4. **Manual Review Queue:** Flag suspicious outputs for admin review

**Validates:** REQ-NFR-055, REQ-NFR-058

### 6.2 API Timeouts

#### 6.2.1 Lambda Timeout Handling

**Scenario:** Lambda function exceeds timeout (10s for resume analysis)

**Prevention:**
```typescript
// Lambda configuration
const resumeAnalysisFunction = new lambda.Function(this, 'ResumeAnalysis', {
  runtime: lambda.Runtime.NODEJS_18_X,
  timeout: Duration.seconds(10),
  memorySize: 2048, // More memory = faster execution
  reservedConcurrentExecutions: 100 // Prevent throttling
});
```

**Recovery:**
```typescript
// API Gateway integration with timeout
const integration = new apigateway.LambdaIntegration(resumeAnalysisFunction, {
  timeout: Duration.seconds(29), // API Gateway max
  proxy: true
});

// Client-side retry logic
async function analyzeResumeWithRetry(
  file: File,
  maxRetries: number = 2
): Promise<ResumeAnalysisResult> {
  for (let attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch('/api/resume/analyze', {
        method: 'POST',
        body: file,
        signal: AbortSignal.timeout(8000) // Client timeout < Lambda timeout
      });
      
      return await response.json();
    } catch (error) {
      if (attempt === maxRetries) throw error;
      
      await sleep(1000 * Math.pow(2, attempt)); // Exponential backoff
    }
  }
}
```

**Validates:** REQ-NFR-001, REQ-NFR-002

#### 6.2.2 External API Failures

**Scenario:** Job market data API (Naukri, Glassdoor) is unavailable

**Detection & Recovery:**
```typescript
async function fetchJobMarketData(
  role: string,
  location: string
): Promise<JobMarketData> {
  const sources = [
    { name: 'Naukri', fetch: fetchNaukriData },
    { name: 'Glassdoor', fetch: fetchGlassdoorData },
    { name: 'AmbitionBox', fetch: fetchAmbitionBoxData }
  ];
  
  const results = await Promise.allSettled(
    sources.map(source => 
      source.fetch(role, location).catch(err => {
        logger.warn(`${source.name} API failed`, { error: err });
        return null;
      })
    )
  );
  
  const successfulResults = results
    .filter(r => r.status === 'fulfilled' && r.value !== null)
    .map(r => (r as PromiseFulfilledResult<JobMarketData>).value);
  
  if (successfulResults.length === 0) {
    // All sources failed - use cached data
    logger.error('All job market APIs failed, using cache');
    return await getCachedMarketData(role, location);
  }
  
  // Aggregate data from successful sources
  return aggregateMarketData(successfulResults);
}
```

**Validates:** REQ-NFR-015

### 6.3 Data Consistency Failures

#### 6.3.1 DynamoDB Transaction Failures

**Scenario:** Transaction fails during multi-item update

**Implementation:**
```typescript
async function updateUserStateTransaction(
  userId: string,
  updates: {
    profile?: Partial<UserProfile>;
    plan?: StudyPlan;
    burnoutRisk?: BurnoutRiskIndex;
  }
): Promise<void> {
  const transactItems: TransactWriteItem[] = [];
  
  if (updates.profile) {
    transactItems.push({
      Update: {
        TableName: MAIN_TABLE,
        Key: { PK: `USER#${userId}`, SK: 'PROFILE' },
        UpdateExpression: 'SET #data = :data, #updated = :updated',
        ExpressionAttributeNames: {
          '#data': 'data',
          '#updated': 'updatedAt'
        },
        ExpressionAttributeValues: {
          ':data': updates.profile,
          ':updated': new Date().toISOString()
        }
      }
    });
  }
  
  try {
    await dynamoClient.transactWriteItems({
      TransactItems: transactItems
    });
  } catch (error) {
    if (error.name === 'TransactionCanceledException') {
      logger.error('Transaction failed, rolling back', { error });
      // DynamoDB automatically rolls back
      throw new Error('Failed to update user state consistently');
    }
    throw error;
  }
}
```

**Validates:** REQ-NFR-016

#### 6.3.2 EventBridge Delivery Failures

**Scenario:** Event fails to trigger downstream service

**Implementation:**
```typescript
// Dead Letter Queue for failed events
const dlq = new sqs.Queue(this, 'EventDLQ', {
  retentionPeriod: Duration.days(14)
});

// EventBridge rule with retry and DLQ
const rule = new events.Rule(this, 'ResumeUpdateRule', {
  eventPattern: {
    source: ['futureforge.resume'],
    detailType: ['Resume Updated']
  }
});

rule.addTarget(new targets.LambdaFunction(trajectoryRecalcFunction, {
  deadLetterQueue: dlq,
  maxEventAge: Duration.hours(2),
  retryAttempts: 3
}));

// Monitor DLQ and alert
const dlqAlarm = new cloudwatch.Alarm(this, 'DLQAlarm', {
  metric: dlq.metricApproximateNumberOfMessagesVisible(),
  threshold: 1,
  evaluationPeriods: 1,
  alarmDescription: 'Events failing to process'
});
```

**Validates:** REQ-NFR-014

### 6.4 Security Failures

#### 6.4.1 Authentication Failures

**Scenario:** Invalid or expired JWT token

**Implementation:**
```typescript
// API Gateway authorizer
const authorizer = new apigateway.CognitoUserPoolsAuthorizer(this, 'Authorizer', {
  cognitoUserPools: [userPool]
});

// Lambda handler with auth validation
async function handler(event: APIGatewayProxyEvent): Promise<APIGatewayProxyResult> {
  try {
    const userId = event.requestContext.authorizer?.claims.sub;
    
    if (!userId) {
      return {
        statusCode: 401,
        body: JSON.stringify({ error: 'Unauthorized' })
      };
    }
    
    // Process request
    
  } catch (error) {
    if (error.name === 'TokenExpiredError') {
      return {
        statusCode: 401,
        body: JSON.stringify({ 
          error: 'Token expired',
          code: 'TOKEN_EXPIRED'
        })
      };
    }
    
    throw error;
  }
}
```

**Validates:** REQ-NFR-019

#### 6.4.2 Rate Limiting

**Scenario:** User exceeds 100 requests/minute

**Implementation:**
```typescript
// API Gateway usage plan
const usagePlan = new apigateway.UsagePlan(this, 'UsagePlan', {
  throttle: {
    rateLimit: 100,
    burstLimit: 200
  },
  quota: {
    limit: 10000,
    period: apigateway.Period.DAY
  }
});

// Custom rate limiter with Redis
async function checkRateLimit(userId: string): Promise<boolean> {
  const key = `ratelimit:${userId}:${Math.floor(Date.now() / 60000)}`;
  
  const count = await redis.incr(key);
  
  if (count === 1) {
    await redis.expire(key, 60); // Expire after 1 minute
  }
  
  if (count > 100) {
    logger.warn('Rate limit exceeded', { userId, count });
    return false;
  }
  
  return true;
}
```

**Validates:** REQ-NFR-021


---

## 7. Testing Strategy

### 7.1 Property-Based Testing

#### 7.1.1 Burnout Logic Testing

**Property:** Recovery Mode must always activate when mood < 2 for 3+ consecutive days

```typescript
import fc from 'fast-check';

describe('Burnout Detection - Property-Based Tests', () => {
  test('Recovery Mode activates on 3+ consecutive low mood days', () => {
    fc.assert(
      fc.property(
        // Generate arbitrary mood sequences
        fc.array(fc.integer(1, 5), { minLength: 7, maxLength: 30 }),
        async (moodSequence) => {
          const userId = `test-${Date.now()}`;
          
          // Setup mood logs
          for (let i = 0; i < moodSequence.length; i++) {
            await burnoutService.processMoodLog(userId, {
              userId,
              date: subDays(new Date(), moodSequence.length - i - 1),
              rating: moodSequence[i] as 1 | 2 | 3 | 4 | 5
            });
          }
          
          // Check recovery mode status
          const status = await burnoutService.checkRecoveryMode(userId);
          
          // Calculate expected result
          let consecutiveLow = 0;
          for (let i = moodSequence.length - 1; i >= 0; i--) {
            if (moodSequence[i] < 2) {
              consecutiveLow++;
            } else {
              break;
            }
          }
          
          const shouldActivate = consecutiveLow >= 3;
          
          // Property assertion
          expect(status.shouldActivate).toBe(shouldActivate);
          
          if (shouldActivate) {
            expect(status.triggers).toContain(
              expect.stringContaining('Low mood')
            );
          }
        }
      ),
      { numRuns: 100 } // Run 100 random test cases
    );
  });
  
  test('Recovery Mode activates when burnout risk > 70%', () => {
    fc.assert(
      fc.property(
        fc.record({
          studyHours: fc.array(fc.integer(0, 16), { minLength: 7, maxLength: 7 }),
          completionRates: fc.array(fc.integer(0, 100), { minLength: 4, maxLength: 4 }),
          moodRatings: fc.array(fc.integer(1, 5), { minLength: 7, maxLength: 7 })
        }),
        async ({ studyHours, completionRates, moodRatings }) => {
          const userId = `test-${Date.now()}`;
          
          // Setup test data
          await setupBurnoutTestData(userId, {
            studyHours,
            completionRates,
            moodRatings
          });
          
          // Calculate burnout risk
          const risk = await burnoutService.calculateBurnoutRisk(userId);
          const status = await burnoutService.checkRecoveryMode(userId);
          
          // Property: If risk > 70%, should activate
          if (risk.overallRisk > 70) {
            expect(status.shouldActivate).toBe(true);
            expect(status.triggers).toContain(
              expect.stringContaining('Burnout risk')
            );
          }
        }
      ),
      { numRuns: 100 }
    );
  });
  
  test('Recovery Mode reduces task load by exactly 50%', () => {
    fc.assert(
      fc.property(
        fc.record({
          weeklyHours: fc.integer(10, 60),
          taskCount: fc.integer(5, 30)
        }),
        async ({ weeklyHours, taskCount }) => {
          const userId = `test-${Date.now()}`;
          
          // Create normal plan
          const normalPlan = await createTestPlan(userId, {
            weeklyHours,
            taskCount
          });
          
          const originalTotalHours = calculateTotalHours(normalPlan);
          
          // Activate recovery mode
          const recoveryPlan = await burnoutService.activateRecoveryMode(
            userId,
            'Test trigger'
          );
          
          const recoveryTotalHours = calculateTotalHours(recoveryPlan);
          
          // Property: Recovery plan should be ~50% of original
          const reductionRatio = recoveryTotalHours / originalTotalHours;
          expect(reductionRatio).toBeGreaterThanOrEqual(0.45);
          expect(reductionRatio).toBeLessThanOrEqual(0.55);
        }
      ),
      { numRuns: 50 }
    );
  });
});
```

**Validates:** REQ-BURN-009, REQ-BURN-011, REQ-BURN-012, REQ-BURN-017

#### 7.1.2 Plan Rebalancing Testing

**Property:** Rebalanced plans must respect hour constraints

```typescript
describe('Plan Rebalancing - Property-Based Tests', () => {
  test('rebalanced plan never exceeds weekly hours by >10%', () => {
    fc.assert(
      fc.property(
        fc.record({
          weeklyHours: fc.integer(10, 60),
          missedTaskCount: fc.integer(1, 10),
          taskDurations: fc.array(fc.integer(30, 240), { minLength: 1, maxLength: 10 })
        }),
        async ({ weeklyHours, missedTaskCount, taskDurations }) => {
          const userId = `test-${Date.now()}`;
          
          // Create plan
          const plan = await createTestPlan(userId, { weeklyHours });
          
          // Create missed tasks
          const missedTasks = taskDurations.slice(0, missedTaskCount).map((duration, i) => ({
            taskId: `task-${i}`,
            title: `Missed Task ${i}`,
            skill: 'DSA',
            estimatedTime: duration,
            difficulty: 'medium' as const,
            status: 'skipped' as const
          }));
          
          // Rebalance
          const rebalanced = await plannerService.rebalancePlan(plan, missedTasks);
          
          // Property: Check all weeks respect constraint
          for (const week of rebalanced.weeklySchedule) {
            const totalMinutes = week.dailyTasks.reduce(
              (sum, task) => sum + task.estimatedTime,
              0
            );
            const totalHours = totalMinutes / 60;
            
            expect(totalHours).toBeLessThanOrEqual(weeklyHours * 1.1);
          }
        }
      ),
      { numRuns: 100 }
    );
  });
  
  test('rebalancing preserves all tasks (no task loss)', () => {
    fc.assert(
      fc.property(
        fc.record({
          originalTaskCount: fc.integer(10, 50),
          missedTaskCount: fc.integer(1, 10)
        }),
        async ({ originalTaskCount, missedTaskCount }) => {
          const plan = await createTestPlanWithTasks(originalTaskCount);
          const missedTasks = plan.weeklySchedule[0].dailyTasks.slice(0, missedTaskCount);
          
          const rebalanced = await plannerService.rebalancePlan(plan, missedTasks);
          
          // Count all tasks in rebalanced plan
          const totalTasks = rebalanced.weeklySchedule.reduce(
            (sum, week) => sum + week.dailyTasks.length,
            0
          );
          
          // Property: Total tasks should be >= original (may extend timeline)
          expect(totalTasks).toBeGreaterThanOrEqual(originalTaskCount);
        }
      ),
      { numRuns: 50 }
    );
  });
});
```

**Validates:** REQ-PLAN-005, REQ-PLAN-006, REQ-PLAN-013

### 7.2 Integration Testing

#### 7.2.1 End-to-End Career Flow

```typescript
describe('Career Flow Integration Tests', () => {
  test('complete user journey: upload → simulate → plan → track', async () => {
    const userId = 'integration-test-user';
    
    // Step 1: Upload resume
    const resumeFile = await loadTestResume('sample-resume.pdf');
    const resumeAnalysis = await resumeService.analyzeResume(resumeFile);
    
    expect(resumeAnalysis.overallScore).toBeGreaterThan(0);
    expect(resumeAnalysis.processingTime).toBeLessThan(5000);
    
    // Step 2: Create profile
    const profile: UserProfile = {
      userId,
      resume: resumeAnalysis.parsedData,
      currentSkills: resumeAnalysis.parsedData.skills,
      experienceLevel: 'fresher',
      learningSpeed: 7,
      weeklyAvailableHours: 30,
      targetRole: 'Backend Developer',
      location: 'Bangalore'
    };
    
    // Step 3: Generate career trajectory
    const trajectory = await simulationService.generateTrajectory(profile);
    
    expect(trajectory.scenarios.bestCase).toBeDefined();
    expect(trajectory.scenarios.averageCase).toBeDefined();
    expect(trajectory.scenarios.worstCase).toBeDefined();
    expect(trajectory.skillGaps.missingSkills.length).toBeGreaterThan(0);
    
    // Step 4: Generate study plan
    const plan = await plannerService.generateRoadmap(
      trajectory.skillGaps,
      profile
    );
    
    expect(plan.weeklySchedule.length).toBeGreaterThan(0);
    expect(plan.milestones.length).toBeGreaterThan(0);
    
    // Step 5: Simulate task completion
    const firstTask = plan.weeklySchedule[0].dailyTasks[0];
    await progressService.completeTask(userId, firstTask.taskId);
    
    // Step 6: Check progress update
    const progress = await progressService.getProgress(userId);
    
    expect(progress.metadata.completedTasks).toBe(1);
    expect(progress.learningVelocity).toBeGreaterThan(0);
  });
  
  test('resume update triggers trajectory recalculation', async () => {
    const userId = 'integration-test-user-2';
    
    // Initial state
    const initialResume = await loadTestResume('initial-resume.pdf');
    const initialAnalysis = await resumeService.analyzeResume(initialResume);
    const initialTrajectory = await simulationService.generateTrajectory({
      userId,
      resume: initialAnalysis.parsedData,
      currentSkills: initialAnalysis.parsedData.skills,
      experienceLevel: 'fresher',
      learningSpeed: 7,
      weeklyAvailableHours: 30,
      targetRole: 'Backend Developer',
      location: 'Mumbai'
    });
    
    const initialSalary = initialTrajectory.scenarios.averageCase.timeline[4].salary.amount;
    
    // Update resume (add projects)
    const updatedResume = await loadTestResume('updated-resume.pdf');
    const updatedAnalysis = await resumeService.analyzeResume(updatedResume);
    
    // Wait for EventBridge to trigger recalculation
    await sleep(6000);
    
    // Fetch new trajectory
    const updatedTrajectory = await getLatestTrajectory(userId);
    const updatedSalary = updatedTrajectory.scenarios.averageCase.timeline[4].salary.amount;
    
    // Expect improvement
    expect(updatedSalary).toBeGreaterThan(initialSalary);
  });
});
```

**Validates:** REQ-INT-001, REQ-INT-002, REQ-INT-004, REQ-INT-009

### 7.3 Load Testing

#### 7.3.1 Concurrent User Simulation

```typescript
import { check } from 'k6';
import http from 'k6/http';

export const options = {
  stages: [
    { duration: '2m', target: 100 },   // Ramp up to 100 users
    { duration: '5m', target: 1000 },  // Ramp up to 1000 users
    { duration: '10m', target: 10000 }, // Ramp up to 10,000 users
    { duration: '5m', target: 0 },     // Ramp down
  ],
  thresholds: {
    http_req_duration: ['p(95)<5000'], // 95% of requests < 5s
    http_req_failed: ['rate<0.01'],    // Error rate < 1%
  },
};

export default function () {
  // Test resume upload
  const resumeFile = open('./test-resume.pdf', 'b');
  const resumeResponse = http.post(
    `${__ENV.API_URL}/resume/analyze`,
    { file: http.file(resumeFile, 'resume.pdf') },
    { headers: { Authorization: `Bearer ${__ENV.AUTH_TOKEN}` } }
  );
  
  check(resumeResponse, {
    'resume analysis status is 200': (r) => r.status === 200,
    'resume analysis time < 5s': (r) => r.timings.duration < 5000,
  });
  
  // Test career simulation
  const simulationResponse = http.post(
    `${__ENV.API_URL}/career/simulate`,
    JSON.stringify({
      targetRole: 'Backend Developer',
      weeklyHours: 30,
      learningSpeed: 7
    }),
    { headers: { 
      'Content-Type': 'application/json',
      Authorization: `Bearer ${__ENV.AUTH_TOKEN}` 
    } }
  );
  
  check(simulationResponse, {
    'simulation status is 200': (r) => r.status === 200,
    'simulation time < 10s': (r) => r.timings.duration < 10000,
  });
}
```

**Validates:** REQ-NFR-007, REQ-NFR-008

### 7.4 Security Testing

#### 7.4.1 Penetration Testing Checklist

```typescript
describe('Security Tests', () => {
  test('SQL injection prevention', async () => {
    const maliciousInput = "'; DROP TABLE users; --";
    
    const response = await request(app)
      .post('/api/profile/update')
      .send({ name: maliciousInput })
      .set('Authorization', `Bearer ${validToken}`);
    
    expect(response.status).not.toBe(500);
    
    // Verify table still exists
    const users = await dynamoClient.scan({ TableName: MAIN_TABLE });
    expect(users.Items).toBeDefined();
  });
  
  test('XSS prevention', async () => {
    const xssPayload = '<script>alert("XSS")</script>';
    
    const response = await request(app)
      .post('/api/profile/update')
      .send({ bio: xssPayload })
      .set('Authorization', `Bearer ${validToken}`);
    
    expect(response.body.bio).not.toContain('<script>');
    expect(response.body.bio).toContain('&lt;script&gt;');
  });
  
  test('rate limiting enforcement', async () => {
    const requests = [];
    
    for (let i = 0; i < 150; i++) {
      requests.push(
        request(app)
          .get('/api/profile')
          .set('Authorization', `Bearer ${validToken}`)
      );
    }
    
    const responses = await Promise.all(requests);
    const rateLimited = responses.filter(r => r.status === 429);
    
    expect(rateLimited.length).toBeGreaterThan(0);
  });
  
  test('authentication required for protected routes', async () => {
    const response = await request(app)
      .get('/api/career/trajectory');
    
    expect(response.status).toBe(401);
  });
  
  test('expired token rejection', async () => {
    const expiredToken = generateExpiredToken();
    
    const response = await request(app)
      .get('/api/profile')
      .set('Authorization', `Bearer ${expiredToken}`);
    
    expect(response.status).toBe(401);
    expect(response.body.code).toBe('TOKEN_EXPIRED');
  });
});
```

**Validates:** REQ-NFR-019, REQ-NFR-021, REQ-NFR-022


---

## 8. AWS Cost Optimization

### 8.1 Serverless Cost Model

**Estimated Monthly Cost for 100,000 Active Users:**

| Service | Usage | Cost |
|---------|-------|------|
| **Lambda** | 50M invocations, 512MB, 2s avg | $83.50 |
| **API Gateway** | 50M requests | $175.00 |
| **DynamoDB** | 10GB storage, 100 WCU, 200 RCU | $61.50 |
| **S3** | 500GB storage, 1M PUT, 10M GET | $23.50 |
| **Bedrock** | 10M tokens (Claude Sonnet) | $300.00 |
| **SageMaker** | 2 ml.t3.medium endpoints | $124.80 |
| **OpenSearch** | 1 t3.small.search instance | $33.12 |
| **ElastiCache** | 1 cache.t3.micro node | $12.41 |
| **CloudFront** | 1TB data transfer | $85.00 |
| **EventBridge** | 10M events | $10.00 |
| **Cognito** | 100K MAU | $275.00 |
| **CloudWatch** | Logs, metrics, alarms | $50.00 |
| **Total** | | **~$1,234/month** |

**Cost per User:** $0.012/month

### 8.2 Cost Optimization Strategies

#### 8.2.1 Lambda Optimization

```typescript
// Use Lambda Provisioned Concurrency for hot functions
const resumeAnalysisFunction = new lambda.Function(this, 'ResumeAnalysis', {
  runtime: lambda.Runtime.NODEJS_18_X,
  memorySize: 2048, // Right-sized based on profiling
  timeout: Duration.seconds(10),
  
  // Provisioned concurrency for predictable latency
  reservedConcurrentExecutions: 10,
  
  // Environment variables for connection reuse
  environment: {
    DYNAMODB_ENDPOINT: process.env.DYNAMODB_ENDPOINT,
    REDIS_ENDPOINT: process.env.REDIS_ENDPOINT
  }
});

// Enable Lambda SnapStart for faster cold starts (Java/Python)
const simulationFunction = new lambda.Function(this, 'Simulation', {
  runtime: lambda.Runtime.PYTHON_3_11,
  snapStart: lambda.SnapStartConf.ON_PUBLISHED_VERSIONS
});
```

**Savings:** 30% reduction in Lambda costs through right-sizing and connection reuse

#### 8.2.2 DynamoDB On-Demand vs Provisioned

```typescript
// Use on-demand for unpredictable workloads
const mainTable = new dynamodb.Table(this, 'MainTable', {
  partitionKey: { name: 'PK', type: dynamodb.AttributeType.STRING },
  sortKey: { name: 'SK', type: dynamodb.AttributeType.STRING },
  
  // On-demand billing for MVP
  billingMode: dynamodb.BillingMode.PAY_PER_REQUEST,
  
  // Switch to provisioned with auto-scaling at scale
  // billingMode: dynamodb.BillingMode.PROVISIONED,
  // readCapacity: 100,
  // writeCapacity: 50,
  
  pointInTimeRecovery: true,
  
  // TTL for auto-expiring old data
  timeToLiveAttribute: 'TTL'
});

// Auto-scaling for provisioned mode
if (mainTable.billingMode === dynamodb.BillingMode.PROVISIONED) {
  const readScaling = mainTable.autoScaleReadCapacity({
    minCapacity: 50,
    maxCapacity: 500
  });
  
  readScaling.scaleOnUtilization({
    targetUtilizationPercent: 70
  });
}
```

**Savings:** 40% cost reduction by switching to provisioned mode at scale

#### 8.2.3 Bedrock Cost Optimization

```typescript
// Cache AI responses to reduce API calls
async function analyzeResumeWithCache(
  resumeContent: string
): Promise<ResumeAnalysisResult> {
  const cacheKey = `resume:${hashContent(resumeContent)}`;
  
  // Check cache first
  const cached = await redis.get(cacheKey);
  if (cached) {
    logger.info('Cache hit for resume analysis');
    return JSON.parse(cached);
  }
  
  // Call Bedrock
  const analysis = await bedrockClient.invokeModel({
    modelId: 'anthropic.claude-3-haiku', // Use cheaper model for simple tasks
    body: JSON.stringify({
      prompt: buildResumePrompt(resumeContent),
      max_tokens: 2000 // Limit token usage
    })
  });
  
  // Cache for 24 hours
  await redis.setex(cacheKey, 86400, JSON.stringify(analysis));
  
  return analysis;
}

// Use cheaper models for appropriate tasks
const modelSelection = {
  resumeAnalysis: 'anthropic.claude-3-haiku',      // $0.25/1M tokens
  careerSimulation: 'anthropic.claude-3-sonnet',   // $3/1M tokens
  complexReasoning: 'anthropic.claude-3-opus'      // $15/1M tokens
};
```

**Savings:** 60% reduction in AI costs through caching and model selection

#### 8.2.4 S3 Intelligent Tiering

```typescript
const resumeBucket = new s3.Bucket(this, 'ResumeBucket', {
  // Intelligent tiering for automatic cost optimization
  lifecycleRules: [
    {
      // Move to Infrequent Access after 30 days
      transitions: [
        {
          storageClass: s3.StorageClass.INFREQUENT_ACCESS,
          transitionAfter: Duration.days(30)
        },
        {
          storageClass: s3.StorageClass.GLACIER,
          transitionAfter: Duration.days(90)
        }
      ],
      // Delete old versions after 180 days
      noncurrentVersionExpiration: Duration.days(180)
    }
  ],
  
  // Enable intelligent tiering
  intelligentTieringConfigurations: [
    {
      name: 'AutoTiering',
      archiveAccessTierTime: Duration.days(90),
      deepArchiveAccessTierTime: Duration.days(180)
    }
  ]
});
```

**Savings:** 50% reduction in S3 costs for older resumes

### 8.3 Monitoring & Cost Alerts

```typescript
// Cost anomaly detection
const costAnomaly = new ce.CfnAnomalyMonitor(this, 'CostAnomaly', {
  monitorName: 'FutureForge-Cost-Monitor',
  monitorType: 'DIMENSIONAL',
  monitorDimension: 'SERVICE'
});

const costAlert = new ce.CfnAnomalySubscription(this, 'CostAlert', {
  subscriptionName: 'FutureForge-Cost-Alert',
  threshold: 100, // Alert if anomaly > $100
  frequency: 'DAILY',
  monitorArnList: [costAnomaly.attrMonitorArn],
  subscribers: [
    {
      type: 'EMAIL',
      address: 'devops@futureforge.ai'
    }
  ]
});

// Budget alerts
const budget = new budgets.CfnBudget(this, 'MonthlyBudget', {
  budget: {
    budgetName: 'FutureForge-Monthly',
    budgetType: 'COST',
    timeUnit: 'MONTHLY',
    budgetLimit: {
      amount: 2000,
      unit: 'USD'
    }
  },
  notificationsWithSubscribers: [
    {
      notification: {
        notificationType: 'ACTUAL',
        comparisonOperator: 'GREATER_THAN',
        threshold: 80 // Alert at 80% of budget
      },
      subscribers: [
        {
          subscriptionType: 'EMAIL',
          address: 'devops@futureforge.ai'
        }
      ]
    }
  ]
});
```

---

## 9. Deployment Architecture

### 9.1 Multi-Environment Strategy

```
┌─────────────────────────────────────────────────────────┐
│                    Development (dev)                     │
│  - Single region (ap-south-1)                           │
│  - Minimal resources                                     │
│  - On-demand billing                                     │
│  - No redundancy                                         │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                    Staging (staging)                     │
│  - Single region (ap-south-1)                           │
│  - Production-like setup                                 │
│  - Load testing enabled                                  │
│  - Automated testing                                     │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                  Production (prod)                       │
│  - Multi-region (ap-south-1 primary, ap-southeast-1 DR) │
│  - High availability                                     │
│  - Auto-scaling enabled                                  │
│  - Full monitoring & alerting                            │
└─────────────────────────────────────────────────────────┘
```

### 9.2 CI/CD Pipeline

```yaml
# .github/workflows/deploy.yml
name: Deploy FutureForge

on:
  push:
    branches: [main, staging, dev]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run unit tests
        run: npm test
      
      - name: Run integration tests
        run: npm run test:integration
      
      - name: Run property-based tests
        run: npm run test:property
  
  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1
      
      - name: Deploy with CDK
        run: |
          npm run cdk deploy -- --all --require-approval never
      
      - name: Run smoke tests
        run: npm run test:smoke
      
      - name: Notify deployment
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: 'Deployment to ${{ github.ref }} completed'
```

### 9.3 Infrastructure as Code (CDK)

```typescript
// lib/futureforge-stack.ts
export class FutureForgeStack extends cdk.Stack {
  constructor(scope: Construct, id: string, props?: cdk.StackProps) {
    super(scope, id, props);
    
    // Environment-specific configuration
    const env = this.node.tryGetContext('env') || 'dev';
    const config = this.node.tryGetContext(env);
    
    // VPC for private resources
    const vpc = new ec2.Vpc(this, 'VPC', {
      maxAzs: config.highAvailability ? 3 : 2,
      natGateways: config.highAvailability ? 2 : 1
    });
    
    // Cognito User Pool
    const userPool = new cognito.UserPool(this, 'UserPool', {
      selfSignUpEnabled: true,
      signInAliases: { email: true },
      passwordPolicy: {
        minLength: 8,
        requireLowercase: true,
        requireUppercase: true,
        requireDigits: true,
        requireSymbols: true
      }
    });
    
    // DynamoDB Table
    const mainTable = new dynamodb.Table(this, 'MainTable', {
      partitionKey: { name: 'PK', type: dynamodb.AttributeType.STRING },
      sortKey: { name: 'SK', type: dynamodb.AttributeType.STRING },
      billingMode: config.useProv provisioned 
        ? dynamodb.BillingMode.PROVISIONED 
        : dynamodb.BillingMode.PAY_PER_REQUEST,
      pointInTimeRecovery: config.highAvailability,
      stream: dynamodb.StreamViewType.NEW_AND_OLD_IMAGES
    });
    
    // Lambda Functions
    const resumeAnalysisFunction = new lambda.Function(this, 'ResumeAnalysis', {
      runtime: lambda.Runtime.NODEJS_18_X,
      handler: 'index.handler',
      code: lambda.Code.fromAsset('lambda/resume-analysis'),
      environment: {
        TABLE_NAME: mainTable.tableName,
        BEDROCK_MODEL: config.bedrockModel
      },
      timeout: Duration.seconds(10),
      memorySize: 2048
    });
    
    mainTable.grantReadWriteData(resumeAnalysisFunction);
    
    // API Gateway
    const api = new apigateway.RestApi(this, 'API', {
      restApiName: `FutureForge-${env}`,
      deployOptions: {
        stageName: env,
        tracingEnabled: true,
        loggingLevel: apigateway.MethodLoggingLevel.INFO
      }
    });
    
    // CloudWatch Dashboard
    new cloudwatch.Dashboard(this, 'Dashboard', {
      dashboardName: `FutureForge-${env}`,
      widgets: [
        [new cloudwatch.GraphWidget({
          title: 'API Requests',
          left: [api.metricCount()]
        })],
        [new cloudwatch.GraphWidget({
          title: 'Lambda Duration',
          left: [resumeAnalysisFunction.metricDuration()]
        })]
      ]
    });
  }
}
```

---

## 10. Monitoring & Observability

### 10.1 CloudWatch Dashboards

```typescript
const dashboard = new cloudwatch.Dashboard(this, 'MainDashboard', {
  dashboardName: 'FutureForge-Production',
  widgets: [
    // Row 1: API Metrics
    [
      new cloudwatch.GraphWidget({
        title: 'API Request Rate',
        left: [api.metricCount({ statistic: 'Sum', period: Duration.minutes(1) })],
        width: 12
      }),
      new cloudwatch.GraphWidget({
        title: 'API Latency (p50, p95, p99)',
        left: [
          api.metricLatency({ statistic: 'p50' }),
          api.metricLatency({ statistic: 'p95' }),
          api.metricLatency({ statistic: 'p99' })
        ],
        width: 12
      })
    ],
    
    // Row 2: Lambda Metrics
    [
      new cloudwatch.GraphWidget({
        title: 'Lambda Invocations',
        left: [
          resumeAnalysisFunction.metricInvocations(),
          simulationFunction.metricInvocations(),
          plannerFunction.metricInvocations()
        ],
        width: 12
      }),
      new cloudwatch.GraphWidget({
        title: 'Lambda Errors',
        left: [
          resumeAnalysisFunction.metricErrors(),
          simulationFunction.metricErrors(),
          plannerFunction.metricErrors()
        ],
        width: 12
      })
    ],
    
    // Row 3: DynamoDB Metrics
    [
      new cloudwatch.GraphWidget({
        title: 'DynamoDB Read/Write Capacity',
        left: [mainTable.metricConsumedReadCapacityUnits()],
        right: [mainTable.metricConsumedWriteCapacityUnits()],
        width: 12
      }),
      new cloudwatch.GraphWidget({
        title: 'DynamoDB Throttles',
        left: [
          mainTable.metricUserErrors({ statistic: 'Sum' })
        ],
        width: 12
      })
    ],
    
    // Row 4: Business Metrics
    [
      new cloudwatch.SingleValueWidget({
        title: 'Active Users (24h)',
        metrics: [new cloudwatch.Metric({
          namespace: 'FutureForge',
          metricName: 'ActiveUsers',
          statistic: 'Sum',
          period: Duration.hours(24)
        })],
        width: 6
      }),
      new cloudwatch.SingleValueWidget({
        title: 'Resumes Analyzed (24h)',
        metrics: [new cloudwatch.Metric({
          namespace: 'FutureForge',
          metricName: 'ResumesAnalyzed',
          statistic: 'Sum',
          period: Duration.hours(24)
        })],
        width: 6
      }),
      new cloudwatch.SingleValueWidget({
        title: 'Recovery Mode Activations (24h)',
        metrics: [new cloudwatch.Metric({
          namespace: 'FutureForge',
          metricName: 'RecoveryModeActivations',
          statistic: 'Sum',
          period: Duration.hours(24)
        })],
        width: 6
      }),
      new cloudwatch.SingleValueWidget({
        title: 'Avg Burnout Risk',
        metrics: [new cloudwatch.Metric({
          namespace: 'FutureForge',
          metricName: 'BurnoutRisk',
          statistic: 'Average',
          period: Duration.hours(24)
        })],
        width: 6
      })
    ]
  ]
});
```

### 10.2 X-Ray Tracing

```typescript
// Enable X-Ray for all Lambda functions
const resumeAnalysisFunction = new lambda.Function(this, 'ResumeAnalysis', {
  tracing: lambda.Tracing.ACTIVE,
  // ... other config
});

// X-Ray SDK in Lambda code
import AWSXRay from 'aws-xray-sdk-core';
const AWS = AWSXRay.captureAWS(require('aws-sdk'));

export async function handler(event: APIGatewayProxyEvent) {
  const segment = AWSXRay.getSegment();
  const subsegment = segment.addNewSubsegment('ResumeAnalysis');
  
  try {
    // Trace Bedrock call
    subsegment.addAnnotation('model', 'claude-3-sonnet');
    const analysis = await analyzeWithBedrock(event.body);
    
    // Trace DynamoDB call
    await dynamoClient.putItem({
      TableName: TABLE_NAME,
      Item: analysis
    });
    
    subsegment.close();
    return { statusCode: 200, body: JSON.stringify(analysis) };
  } catch (error) {
    subsegment.addError(error);
    subsegment.close();
    throw error;
  }
}
```

---

## 11. Security Architecture

### 11.1 Defense in Depth

```
┌─────────────────────────────────────────────────────────┐
│ Layer 1: Edge Protection (CloudFront + WAF)             │
│  - DDoS protection                                       │
│  - Geo-blocking                                          │
│  - Rate limiting                                         │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│ Layer 2: API Gateway                                     │
│  - Authentication (Cognito)                              │
│  - Authorization (IAM)                                   │
│  - Request validation                                    │
│  - Throttling                                            │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│ Layer 3: Lambda Functions                                │
│  - Input sanitization                                    │
│  - Business logic validation                             │
│  - Least privilege IAM roles                             │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│ Layer 4: Data Layer                                      │
│  - Encryption at rest (KMS)                              │
│  - Encryption in transit (TLS 1.3)                       │
│  - VPC isolation                                         │
│  - Backup & recovery                                     │
└─────────────────────────────────────────────────────────┘
```

**Validates:** REQ-NFR-017 through REQ-NFR-024

---

## 12. Disaster Recovery

### 12.1 Backup Strategy

- **DynamoDB:** Point-in-time recovery (35 days), daily backups to S3
- **S3:** Cross-region replication to ap-southeast-1
- **RTO:** 4 hours
- **RPO:** 1 hour

### 12.2 Failover Procedure

1. Route53 health checks detect primary region failure
2. Automatic DNS failover to DR region
3. Lambda functions deployed in both regions
4. DynamoDB global tables for data replication

---

## Appendix A: Requirement Validation Matrix

| Requirement ID | Design Component | Validation Method |
|---------------|------------------|-------------------|
| REQ-SIM-006 | CareerSimulationService | Unit + Integration Tests |
| REQ-RES-002 | ResumeIntelligenceService | Performance Tests (p95 < 5s) |
| REQ-PLAN-013 | AdaptivePlannerService | Property-Based Tests |
| REQ-BURN-016 | BurnoutMonitorService | Property-Based Tests |
| REQ-NFR-001 | Lambda + Bedrock | Load Tests |
| REQ-NFR-007 | Auto-scaling | Load Tests (10K concurrent) |
| REQ-NFR-025 | Mumbai region deployment | Infrastructure validation |

---

**Document Version:** 1.0  
**Last Updated:** February 14, 2026  
**Status:** Ready for Implementation  
**Target:** Amazon AI for Bharat Hackathon

*End of Technical Design Document*

