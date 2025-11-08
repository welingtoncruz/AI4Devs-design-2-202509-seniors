# MVP Epic Catalog – WJC

## 1. Overview
- Objective: frame the LTI MVP around customer value delivery so we can validate the product quickly and iteratively.
- Reference inputs: `LTI-WJC/LTI-WJC.md`, `framework/knowledge_bases/application.md`, prior user stories in `UserStories-WJC.md`.
- Guiding principles:
  - Lead with user problems and measurable outcomes.
  - Bias toward features that shorten the hiring cycle for recruiters and hiring managers.
  - Keep scope small enough to validate desirability and viability before heavy scaling.

## 2. MVP Epic Map (Value-First)

| Epic ID | Name | Primary Persona | Core Value Delivered | Validation Signal |
| --- | --- | --- | --- | --- |
| EP-01 | Fast Team Onboarding & Governance | HR Admin | Launch team in minutes with proper access and pipelines | Time-to-activate org ≤ 1 day |
| EP-02 | AI-Guided Candidate Prioritisation | Recruiter | Cut screening time by surfacing best-fit applicants first | 40% reduction in manual screening time |
| EP-03 | Collaborative Hiring Decisions | Recruiter & Hiring Manager | Align stakeholders through shared evaluations and feedback | ≥80% candidates reviewed collaboratively |
| EP-04 | Frictionless Interview Coordination | Recruiter & Candidate | Remove scheduling back-and-forth and keep everyone informed | 70% reduction in scheduling cycles |
| EP-05 | Candidate Engagement & Communication | Candidate & Recruiter | Provide consistent updates that improve candidate experience | 90% of candidates receive status updates within 48h |
| EP-06 | Insight & Trust Foundations | Leadership & Compliance | Offer actionable metrics and auditability to build trust | Baseline dashboard for pipeline metrics & bias reports |

## 3. Epic Details

### EP-01 · Fast Team Onboarding & Governance
- **Problem**: New customers struggle to set up ATS access, roles, and pipelines quickly, delaying value realization.
- **Value Proposition**: Allow HR admins to self-provision the workspace with minimal friction, ensuring the team is productive and compliant from day one.
- **Key Capabilities**:
  - Organization registration with guided onboarding checklist.
  - Role-based invitations (admin/recruiter/hiring manager) and status tracking.
  - Default hiring pipeline templates configurable per organization.
  - Audit log of critical actions for trust and compliance.
- **User Outcomes**:
  - HR admins activate their team without external support.
  - Recrutadores entram com fluxos padronizados prontos.
- **Success Metrics**:
  - 90% of new orgs complete onboarding within 24h.
  - <5% of invite emails fail or remain pending after 48h.
  - NPS ≥ 8 for onboarding feedback.
- **Dependencies**: Notification service for invitations; RBAC framework.
- **Future Story Seeds**:
  - Self-service billing and subscription management.
  - Org-wide settings for diversity goals and compliance options.

### EP-02 · AI-Guided Candidate Prioritisation
- **Problem**: Recruiters lose time parsing resumes and ranking candidates manually.
- **Value Proposition**: Automatically parse, score, and rank applicants by job-specific criteria while signalling potential bias.
- **Key Capabilities**:
  - Structured job intake capturing must-have skills, experience, and weightings.
  - AI screening pipeline producing scores, competency highlights, and bias indicators.
  - Continuous learning via recruiter feedback and overrides.
- **User Outcomes**:
  - Shortlist creation shifts from hours to minutes.
  - Recruiters trust AI signals thanks to transparency and control.
- **Success Metrics**:
  - ≥40% reduction in time spent on initial screening.
  - ≥70% of new applications processed by AI within 5 minutes.
  - Bias report available for 100% of scored cohorts.
- **Dependencies**: EP-01 for job pipeline defaults; access to AI/LLM services and document storage.
- **Future Story Seeds**:
  - Cross-job candidate recommendations.
  - Auto-generated outreach templates based on candidate profile.

#### EP-02 User Stories & Sprint Slices

##### US02-01 · Structured Job Intake With AI Configuration
- Persona: Recruiter  
- Story: As a recruiter, I want to capture structured job requirements and tune AI screening weights so that the system can evaluate applicants according to what this role needs most.  
- Acceptance Criteria:
  1. Job creation supports mandatory fields (title, department, location, employment type, salary range) with validation and autosave.
  2. AI configuration panel allows selecting must-have skills, minimum experience, and sliders for weighting criteria; persisted per job.
  3. Changes to AI settings emit versioned events and are immediately applied to subsequent screenings without republishing the job.
- Estimate: **M (18h)** — backend schema + contract work plus UI flows with autosave.
- Sprint Target: **MVP Sprint 1** to unlock downstream automation.
- Subtasks:
  - Extend job schema & migrations for structured requirements and AI config metadata.
  - Build recruiter UI flow (web) with autosave drafts and validation parity.
  - Wire save/update endpoints to notify AI Screening Service via contract-tested payloads.
  - Instrument audit log entries for configuration changes.

##### US02-02 · Automated Resume Parsing & Scoring Pipeline
- Persona: Recruiter  
- Story: As a recruiter, I want new applications to be parsed and scored automatically so that qualified candidates surface without manual review.  
- Acceptance Criteria:
  1. When an application is submitted, resumes are parsed to extract skills, experience, and education.
  2. AI Screening Service computes composite scores using job-specific weights within 5 minutes of submission.
  3. Parsed data and scores persist in candidate records and are visible on the job pipeline dashboard.
- Estimate: **L (26h)** — ingestion worker, service integration, UI surface, monitoring.
- Sprint Target: **MVP Sprint 1 → 2** (starts in Sprint 1 once US02-01 is stable, finishes early Sprint 2).
- Subtasks:
  - Implement ingestion worker consuming application events and invoking AI Screening Service.
  - Store extracted metadata (skills, years experience) in structured tables/JSON.
  - Display ranked list (score, highlights, confidence) in recruiter pipeline view.
  - Alert on failures (logging + notification) if scoring exceeds SLA.

##### US02-03 · Recruiter Feedback & Override Loop
- Persona: Recruiter  
- Story: As a recruiter, I want to adjust AI scores and give feedback when rankings feel off so that the system learns and reflects human judgment.  
- Acceptance Criteria:
  1. Recruiters can apply score adjustments with required rationale; the override is logged and visible to collaborators.
  2. Overrides influence subsequent sorting immediately in the UI.
  3. Feedback events feed a learning dataset that can be exported for model updates.
- Estimate: **M (20h)** — UI controls, persistence, deterministic ranking blend, export pipeline.
- Sprint Target: **MVP Sprint 2** after scoring pipeline is live.
- Subtasks:
  - Add UI controls for score override and comment capture.
  - Persist override history with actor, timestamp, delta, rationale.
  - Update ranking service to merge AI score and override weight deterministically.
  - Create export job/API for feedback data snapshots.

##### US02-04 · Bias Transparency & Safeguards
- Persona: Compliance-conscious Recruiter / HR Lead  
- Story: As a hiring leader, I want visibility into potential bias in AI screening so that we can take corrective action and maintain fair hiring practices.  
- Acceptance Criteria:
  1. Bias reports (representation, score distribution) generate alongside each screening batch.
  2. Recruiters receive alerts when bias thresholds breach configured guardrails.
  3. Reports are accessible in the analytics hub with export to CSV/PDF.
- Estimate: **M (22h)** — AI contract updates, analytics persistence, alerting, visualizations.
- Sprint Target: **MVP Sprint 3** once scoring and feedback loops are reliable.
- Subtasks:
  - Extend AI response contract to include bias metrics and guardrail thresholds.
  - Persist bias summaries linked to job + screening batch for analytics retrieval.
  - Build notifications (in-app/email) triggered by threshold breaches.
  - Surface bias dashboards with filters (job, timeframe) and export options.

##### EP-03 MVP Story · Candidate Pipeline Overview with AI Signals
- Persona: Recruiter  
- Story: As a recruiter, I want to view a pipeline board highlighting AI scores and statuses so that I can action the top candidates quickly.  
- Acceptance Criteria:
  1. Pipeline view lists stages with candidate cards showing AI score, bias flag, and last activity.
  2. Sorting and filtering by AI score, override status, and job stage are available.
  3. Clicking a candidate opens a summary panel with resume highlights and action buttons (shortlist, reject, comment).
- Estimate: **M (18h)** — UI components, integration with scoring data, filtering logic.
- Sprint Target: **MVP Sprint 1** alongside initial AI configuration work.
- Technical Requirements:
  - API endpoints delivering candidate + score metadata per stage (REST/GraphQL).
  - Frontend state management for filters/sorting optimized for MVP performance.
  - Telemetry for view usage and key actions to inform iteration.

### EP-03 · Collaborative Hiring Decisions
- **Problem**: Hiring decisions stall due to misaligned feedback, lost context, and siloed evaluations.
- **Value Proposition**: Provide a shared workspace where recruiters and hiring managers evaluate candidates together, speeding consensus.
- **Key Capabilities**:
  - Candidate dashboard with real-time updates, threaded comments, and evaluation scores.
  - Consensus workflow (voting, decision thresholds, escalation paths).
  - Visibility into feedback from interviews, assessments, and AI rankings.
- **User Outcomes**:
  - Hiring manager feedback loops shrink from days to hours.
  - Decision-making becomes transparent and data-driven.
- **Success Metrics**:
  - ≥80% of shortlisted candidates reviewed by both recruiter and hiring manager.
  - Median decision time from shortlist to outcome ≤ 3 days.
  - Stakeholder satisfaction (survey) ≥ 8/10 on collaboration experience.
- **Dependencies**: EP-02 for candidate data richness; real-time collaboration tools (e.g., websockets).
- **Future Story Seeds**:
  - Mobile experience for approvals.
  - Structured interview rubrics and scorecards.

### EP-04 · Frictionless Interview Coordination
- **Problem**: Interview scheduling consumes recruiter time and creates candidate frustration.
- **Value Proposition**: Automate the discovery of overlapping availability, messenger notifications, and reminders to keep interviews on track.
- **Key Capabilities**:
  - Calendar integrations (Google/Microsoft) to retrieve interviewer availability.
  - Candidate self-service slot selection with fallback workflows.
  - Automated reminders, prep kits, and contingency handling (cancellations, backups).
- **User Outcomes**:
  - Recruiters shift focus from logistics to high-value coaching.
  - Candidates experience faster, clearer scheduling.
- **Success Metrics**:
  - 70% reduction in back-and-forth scheduling emails.
  - ≥90% of interviews confirmed within 24h of candidate readiness.
  - No-show rate reduced by 20% via reminders.
- **Dependencies**: EP-01 invites to set up interviewers; Notification service; integration credentials for calendar APIs.
- **Future Story Seeds**:
  - Multi-interview day orchestration.
  - Interviewer load balancing and fairness alerts.

### EP-05 · Candidate Engagement & Communication
- **Problem**: Lack of timely, personalized updates leads to drop-offs and poor candidate experience.
- **Value Proposition**: Provide automated yet human-feeling communication across the hiring funnel.
- **Key Capabilities**:
  - Communication templates tied to pipeline stages (email/SMS).
  - Event-driven notifications for status changes, interview prep, and decisions.
  - Candidate self-service portal for status, scheduling, and feedback requests.
- **User Outcomes**:
  - Candidates feel informed and valued, reducing ghosting.
  - Recruiters maintain consistent tone with minimal manual effort.
- **Success Metrics**:
  - 90% of candidates receive status updates within 48h of stage change.
  - Candidate satisfaction CSAT ≥ 4/5 on experience surveys.
  - Drop-off/ghosting rate decreases by 30%.
- **Dependencies**: EP-02 and EP-04 events to trigger messages; Notification service channels.
- **Future Story Seeds**:
  - Two-way SMS for quick clarifications.
  - Employer branding customization per job.

### EP-06 · Insight & Trust Foundations
- **Problem**: Leadership needs visibility into funnel health and fairness to justify continued investment.
- **Value Proposition**: Deliver baseline analytics and auditing so leaders can measure outcomes and ensure compliance.
- **Key Capabilities**:
  - Dashboard with pipeline metrics (time-to-hire, stage conversion, source performance).
  - Bias summaries aggregated from AI screening outcomes.
  - Audit trails for key actions (role changes, configuration updates).
- **User Outcomes**:
  - Leadership can demonstrate ROI and compliance from early usage.
  - Teams detect bottlenecks quickly.
- **Success Metrics**:
  - Executive dashboard adoption ≥ 75% of pilot customers.
  - Ability to export audit log within SLA (e.g., ≤5 minutes).
  - Identification and remediation of at least one process bottleneck during MVP pilot.
- **Dependencies**: Data emitted from previous epics; lightweight analytics store.
- **Future Story Seeds**:
  - Predictive hiring forecasts.
  - Diversity compliance reports and alerts.

## 4. Next Steps
- Validate epic scope with design & engineering leads to size MVP slices.
- Derive user stories (value-focussed) per epic prioritising EP-01 and EP-02 for initial sprint planning.
- Define MVP success metrics instrumentation plan (log events, dashboards, surveys).
- Align GTM and customer success on pilot criteria using these epics.

## 5. Engineering & Data Science Alignment Notes
- Schedule a joint review with Engineering + Data Science to confirm:
  - AI Screening Service contract changes (weights, bias metrics, feedback events) are feasible and versioned.
  - Available model capabilities meet SLA (≤5 min scoring) and bias-reporting expectations.
  - Data retention and privacy requirements for candidate skill extraction comply with policy.
- Define handshake artefacts (API schemas, event payloads, test fixtures) before Sprint 1 kickoff.
- Capture follow-up actions and update story subtasks as agreements evolve.

## 6. Sprint 0 Enablement Plan

| Workstream | Objectives | Key Activities | Owner(s) | Deliverables |
| --- | --- | --- | --- | --- |
| Product & Delivery | Align backlog and definition of done for MVP Sprints 1–3 | Finalize EP-01/EP-02 story acceptance criteria, confirm success metrics, prepare release roadmap, set up sprint rituals | PM, Delivery Lead | Story map, sprint calendar, communication plan |
| Architecture & Environments | Prepare infrastructure foundations for dev/test | Provision initial environments (dev/staging), configure hosting (Render/Fly/Heroku), set up CI/CD pipelines, define env vars/secrets management | Tech Lead, DevOps | Infra diagram, CI pipeline config, environment readiness checklist |
| Data & AI Contracts | Guarantee AI Screening integration readiness | Document API schemas & payloads, align on bias metrics, define fallback behaviours, secure API keys and quotas | Data Science Lead, Backend Lead | API contract doc, mock responses, rate-limit plan |
| Security & Compliance | Establish baseline governance | Implement RBAC policy baseline, configure audit logging framework, define data retention policies, complete DPIA/lightweight privacy review | Security/Compliance, Backend Lead | RBAC matrix, logging standards, compliance checklist |
| UX & Research | Validate key flows and content | Produce lo-fi/hi-fi prototypes for job intake, candidate ranking, bias alerts; run quick usability review with pilot users; finalize copy for onboarding emails | Product Designer, PM | Prototype deck, UX findings, content guide |
| Observability & QA | Ensure quality gates are ready | Set up logging/monitoring tools (Papertrail, Better Stack), define alert thresholds, create test strategy, prep seed data and fixtures | QA Lead, DevOps | Monitoring dashboards, test plan, seed dataset |
| GTM & Customer Success | Prep pilot support | Identify pilot customers, define success criteria, craft onboarding/training material, align support channels | CS Lead, PM | Pilot roster, training assets, support playbook |

### Sprint 0 Exit Criteria
- Environments (dev/staging) deployed with CI/CD running sample builds.
- AI Screening Service contract signed off with mock tests passing.
- UX prototypes validated and ready for Sprint 1 implementation handoff.
- Monitoring/logging baseline active; alert thresholds defined.
- Pilot customers selected and success metrics agreed.
- All EP-01/EP-02 stories groomed with estimates and dependencies captured.

## 7. Sprint 0 Epics & User Stories (Tech-Ready)

### S0-EP1 · Foundations Platform Setup
- **Purpose**: Guarantee environments, access, and governance exist before Sprint 1 build work.

#### S0-US1 · Provision Dev & Staging Environments
- Story: As a tech lead, I need dedicated dev/staging environments so the team can deploy and test MVP code safely.  
- Acceptance Criteria:
  1. Dev and staging environments deployed (Render/Fly/Heroku) with baseline health checks.
  2. CI/CD pipeline builds main branch and auto-deploys to dev on merge; staging requires approval.
  3. Environment variables and secrets managed via platform vault with rotation policy documented.
- Estimate: **M (18h)** — infra provisioning, pipeline setup, automation scripts.
- Technical Requirements:
  - Infrastructure-as-code or documented scripts for environment creation.
  - GitHub Actions workflow with build/test/deploy jobs, caching enabled.
  - Health check endpoint (`/healthz`) and monitoring hook (Better Stack/Papertrail).

#### S0-US2 · Configure RBAC and Access Governance
- Story: As a security owner, I want RBAC foundations in place so early users access only their organization data.  
- Acceptance Criteria:
  1. Role matrix (admin, recruiter, hiring manager, system) defined and implemented in auth layer.
  2. Tenant isolation tests pass demonstrating org-specific data boundaries.
  3. Audit logging captures login, role change, invitation, and config update events.
- Estimate: **M (20h)** — policy design, middleware, tests, logging integration.
- Technical Requirements:
  - Middleware/integration tests enforcing claims-based authorization.
  - Log shipping to central storage with retention ≥ 30 dias.
  - Playbook for incident response and access revocation.

### S0-EP2 · AI Integration Readiness
- **Purpose**: Ensure the AI Screening Service can be integrated without blockers in Sprint 1.

#### S0-US3 · Finalize AI Service Contract & Sandbox
- Story: As a backend engineer, I need agreed API schemas and sandbox endpoints to integrate AI screening reliably.  
- Acceptance Criteria:
  1. Request/response schemas for screening, feedback, and bias metrics signed-off by DS/Eng.
  2. Sandbox environment reachable with auth keys stored securely; rate limits documented.
  3. Contract tests added to CI verifying payloads against schema.
- Estimate: **M (16h)** — documentation, schema validation, sandbox configuration.
- Technical Requirements:
  - OpenAPI/JSON Schema docs versioned in repo.
  - Pact or custom schema validation tests in CI pipeline.
  - Rotating API keys stored in secret manager.

#### S0-US4 · Build Synthetic Data & Fixtures
- Story: As a data scientist, I need realistic sample resumes and job configurations so we can test screening flows end-to-end.  
- Acceptance Criteria:
  1. Fixture dataset (≥20 resumes + job configs) stored in secured bucket/source control with anonymized data.
  2. Scripts to seed dev/staging databases with baseline organizations, jobs, and applications.
  3. Synthetic data covers edge cases (missing fields, international formats, diverse demographics) for bias testing.
- Estimate: **S (12h)** — data crafting, seeding scripts, documentation.
- Technical Requirements:
  - ETL/seed scripts (Python/SQL) idempotent and runnable via CI/CD job.
  - Storage bucket with restricted access (S3/B2) and documented retention.
  - Data catalog documenting fields, assumptions, and intended use.

### S0-EP3 · Experience & Communication Foundations
- **Purpose**: Prepare UX artifacts and communication assets to avoid rework in Sprint 1.

#### S0-US5 · Validate Key UX Flows
- Story: As a product designer, I need validated prototypes for job intake and ranking so we can build with confidence.  
- Acceptance Criteria:
  1. Lo-fi → hi-fi prototypes reviewed with at least 3 pilot users; feedback documented.
  2. Final copy for forms, tooltips, and bias messaging approved.
  3. Accessibility checks (contrast, keyboard navigation) completed on prototypes.
- Estimate: **S (10h)** — prototipagem, entrevistas rápidas, ajustes.
- Technical Requirements:
  - Figma (ou similar) prototypes linked in project docs.
  - UX research notes stored in shared space (Confluence/Notion).
  - Accessibility checklist filled e revisada pelo design.

#### S0-US6 · Prepare Communication Channels
- Story: As a customer success lead, I need onboarding and notification templates ready so users receive consistent messaging during tests.  
- Acceptance Criteria:
  1. Email templates for invites, activation, and status updates drafted and QA’d.
  2. Notification service (Postmark/Mailgun) configured with verified domain, DKIM/SPF.
  3. Support channel (Slack/shared inbox) established with response SLA defined.
- Estimate: **S (8h)** — criação de templates, setup do provedor, testes de entrega.
- Technical Requirements:
  - Template repository (MJML/Handlebars) versioned in repo.
  - DNS records updated and verified for chosen email provider.
  - Monitoring on delivery failure rates with alert thresholds.

### Sprint 0 Tracking
- KPI: Sprint 0 completion = 100% user stories above marked “Done”.
- Meeting cadence: Weekly steering + daily async check-ins via project channel.
- Risks: Delays in AI contract or environment provisioning can push Sprint 1 start; track in RAID log.

## EP-01 · Fast Team Onboarding & Governance (MVP Stories)

### EP01-US1 · Self-Service Organization Setup
- Persona: HR Administrator  
- Story: As an HR administrator, I want to register my organization and configure a baseline pipeline so that my team can start hiring immediately.  
- Acceptance Criteria:
  1. Registration flow captures organization details, admin credentials, and terms acceptance; success leads to admin dashboard.
  2. Admin selects or customizes a default hiring pipeline template applied to new jobs.
  3. Platform sends confirmation email and logs onboarding completion.
- Estimate: **M (18h)** — backend creation, onboarding UI, template configuration.
- Sprint Target: **MVP Sprint 1** (P0).
- Technical Requirements:
  - API endpoints for organization creation and pipeline template setup.
  - Frontend wizard with persisted progress and validation parity with backend.
  - Integration with Notification service for confirmation plus analytics event tracking.

### EP01-US2 · Team Invitation & Activation
- Persona: HR Administrator  
- Story: As an admin, I want to invite recruiters and hiring managers so they can access the system with correct permissions.  
- Acceptance Criteria:
  1. Invite form supports email entry, role assignment, and optional welcome message.
  2. Invitation email contains secure activation link; status tracked (pending, activated).
  3. Activated users land on role-appropriate dashboard; access leverages RBAC enforced rules.
- Estimate: **S (12h)** — invitation workflow, email templates reuse, RBAC hookup.
- Sprint Target: **MVP Sprint 1** (P0).
- Technical Requirements:
  - Invitation service integrated with S0-US2 RBAC foundations.
  - Template and provider configuration (reusing Sprint 0 assets).
  - Audit log of invitations and activations.

## EP-02 · AI-Guided Candidate Prioritisation (Additional Story)

### US02-05 · Candidate Intake & Import MVP
- Persona: Recruiter  
- Story: As a recruiter, I want to add candidates manually or via CSV so that I can feed the AI pipeline before integrations are built.  
- Acceptance Criteria:
  1. Recruiters can create candidate/application manually with resume upload and key fields.
  2. CSV import (template provided) ingests multiple candidates, reporting validation errors inline.
  3. Imported candidates trigger the same AI screening process and appear in pipeline view.
- Estimate: **M (20h)** — upload handling, validation, job association, AI trigger.
- Sprint Target: **MVP Sprint 1** (P1 high value).
- Technical Requirements:
  - File storage integration for resumes (leveraging Sprint 0 infra).
  - Background job to process CSV rows and trigger application events.
  - Error reporting and retry mechanism for failed rows.

## 8. Consolidated Backlog & Prioritization

### Priority Key
- P0 – critical to unlock next sprint / risk mitigation.
- P1 – high value for MVP flow, scheduled soon.
- P2 – important but can follow initial validation.

### Backlog Table
| Priority | Sprint Target | Story ID | Epic | Estimate (T-shirt / h) | Notes |
| --- | --- | --- | --- | --- | --- |
| P0 | Sprint 0 | S0-US1 | S0-EP1 | M / 18 | Environments and pipeline foundation. |
| P0 | Sprint 0 | S0-US3 | S0-EP2 | M / 16 | AI contract approval avoids integration blockers. |
| P0 | Sprint 0 | S0-US2 | S0-EP1 | M / 20 | RBAC & audit to protect tenant data. |
| P0 | Sprint 0 | S0-US4 | S0-EP2 | S / 12 | Fixtures to test AI pipeline early. |
| P0 | Sprint 0 | S0-US5 | S0-EP3 | S / 10 | UX validated before build. |
| P0 | Sprint 0 | S0-US6 | S0-EP3 | S / 8 | Comms ready for pilots. |
| P0 | Sprint 1 | EP01-US1 | EP-01 | M / 18 | Onboarding flow enables first-value moment. |
| P0 | Sprint 1 | EP01-US2 | EP-01 | S / 12 | Team activation required for collaboration. |
| P1 | Sprint 1 | US02-01 | EP-02 | M / 18 | Job intake + AI config – unlocks automation. |
| P1 | Sprint 1 | US02-05 | EP-02 | M / 20 | Candidate intake ensures data flow day one. |
| P1 | Sprint 1 | EP-03 MVP | EP-03 | M / 18 | Pipeline overview reveals AI insights quickly. |
| P1 | Sprint 1 → 2 | US02-02 | EP-02 | L / 26 | Scoring pipeline; depends on US02-01, S0 stories. |
| P1 | Sprint 2 | US02-03 | EP-02 | M / 20 | Feedback loop once scoring live. |
| P1 | Sprint 3 | US02-04 | EP-02 | M / 22 | Bias transparency builds trust. |
| P2 | Sprint 2+ | Additional EP-01 stories | EP-01 | TBD | Extensões (billing, compliance) após MVP básico. |
| P2 | Sprint 2+ | Additional EP-03 stories | EP-03 | TBD | Colaboração avançada depois do MVP board. |
| P2 | Sprint 2+ | EP-04 stories | EP-04 | TBD | Scheduling after core screening. |
| P2 | Sprint 2+ | EP-05 stories | EP-05 | TBD | Candidate comms once pipeline stable. |
| P2 | Sprint 3+ | EP-06 stories | EP-06 | TBD | Analytics go-live after data capture. |

### Backlog Notes
- Sprint 0 concentrates on enabling workstreams (infra, AI, UX, comms) — all flagged P0.
- Sprint 1 entrega o “primeiro valor” end-to-end: onboarding (EP01-US1/US2), intake configurável (US02-01/US02-05) e pipeline overview (EP-03 MVP); iniciar US02-02 ao final.
- Sprint 2 continua com scoring completo (US02-02) e feedback loop (US02-03); 
- Sprint 3 adiciona bias transparency (US02-04) e evoluções.
- Reavaliar estimativas das histórias de colaboração/scheduling/analytics conforme dados das sprints iniciais.
- Manter flexibilidade para puxar itens P2 conforme feedback dos pilotos.

