Assignment: Learning Claude Architecture + Creating Agents
​Course: AI Systems & Agentic Workflows
Topic: Understanding Claude's Architecture and Agent Creation  
​Task A: Architecture Write-up (The Full Stack)
​Claude-based workflows are structured as a layered system where reasoning is separated from execution.  
​1. The Five Layers
​.claude.md (Global Rules): Defines "how to think," reasoning habits, tone, safety, and uncertainty handling.  
​skill/ (Specialized Capabilities): A collection of modules, templates, and tool wrappers that guide Claude through specific tasks.  
​Claude Engine (Reasoning + Coordination): The core executor that interprets intent, plans steps, selects tools, and manages constraints.  
​Tools (Hands): External capabilities like the file system, shell commands, and data retrieval.  
​User (Director): Provides the initial intent, goals, constraints, and acceptance criteria.  
​2. Architecture Diagram

graph TD
    User[User: Director] -->|Provides Intent| Engine[Claude Engine: Reasoning/Coordination]
    Engine <-->|Consults Rules| Rules[.claude.md: Global Instructions]
    Engine <-->|Utilizes| Skills[skill/ folder: Dynamic Resources]
    Engine <-->|Executes via| Tools[Tools: Shell/Files/API]

Task B: "How Claude Thinks" via .claude.md
​Below is a sample .claude.md configuration for a technical organization.  

# Organization Rules: NexusTech

## 1. Tone and Style
- [span_9](start_span)Use concise, technical language[span_9](end_span).
- [span_10](start_span)Avoid conversational fluff; prioritize direct answers[span_10](end_span).

## 2. Reasoning Constraints
- **[span_11](start_span)Verify Assumptions:** Always state assumptions before taking destructive actions[span_11](end_span).
- **[span_12](start_span)Missing Inputs:** Explicitly ask the user for missing data rather than guessing[span_12](end_span).
- **[span_13](start_span)Checklists:** Use checklists for multi-step execution[span_13](end_span).

## 3. Tool Usage Policy
- **[span_14](start_span)Builds:** Use the shell tool for builds and testing[span_14](end_span).
- **[span_15](start_span)Protection:** Do not delete files without explicit user confirmation[span_15](end_span).
- **[span_16](start_span)Audit:** Log all shell commands before execution[span_16](end_span).

## 4. Safety Guardrails
- **[span_17](start_span)Secrets:** Redact API keys or passwords from output[span_17](end_span).
- **[span_18](start_span)Compliance:** Ensure all generated code meets internal security standards[span_18](end_span).

## 5. Output Format
- [span_19](start_span)Every response must include a "Summary" and "Action Items" section[span_19](end_span).

Task C: Skill Design (skill/modules)

​Skill 1: skill/triage
​Purpose: Prioritizes issues and produces a remediation plan.
​Inputs: Bug report description, repository link.
​Outputs: Priority level (High/Med/Low), assigned team, suggested fix steps.
​Tool Usage: read_file, shell (to check logs).
​Steps: 1. Analyze logs; 2. Compare against known bugs; 3. Score severity.
​Failure Modes: Vague bug report. Mitigation: Ask follow-up questions.
​Test Case: Input a database crash report; verify it outputs "Priority: High."
​Skill 2: skill/doc_summarize
​Purpose: Summarizes a technical spec into core requirements.
​Inputs: Document file path.
​Outputs: Summary, Functional Requirements, Technical Constraints.
​Steps: 1. Read document; 2. Identify keywords; 3. Structure into bullet points.
​Test Case: Provide a 10-page spec; verify the summary is under 500 words.
​Skill 3: skill/audit_crypto
​Purpose: Scans code/config for insecure crypto usage.
​Inputs: Directory path.
​Tools: grep, ls.
​Failure Modes: False positives in comments. Mitigation: Use regex to target active code only.
​Test Case: Scan a file with MD5; verify the skill flags it as insecure.

​Task D: Claude Engine: Coordination & Validation
​The Claude engine manages the "thinking" process through specific internal roles:  
​Planner: Decomposes a large task into smaller, executable steps.  
​Router: Chooses the correct skill or tool for each sub-task.  
​Checker: Validates that the output meets the requirements of .claude.md.  
​Workflow Trace Example:
​Intent: "Fix the authentication bug."
​Step 1: Engine uses shell to list files in /auth.  
​Step 2: Engine calls skill/triage to identify the error pattern.  
​Step 3: Engine reads the specific file causing the error.  
​Step 4: Engine proposes a fix and asks User for confirmation (Ambiguity Handling).  
​Step 5: Engine applies the fix and runs a test script.  

​Task E & F: Creating Agents in Claude
​Agent 1: The Security Auditor
​Agent Name: Security Sentinel.
​Purpose: Ensure all code adheres to security standards.
​Responsibilities: Scan for hardcoded secrets, check crypto libraries, and audit dependency versions.
​Out-of-Scope: Fixing functional UI bugs or writing feature code.
​Inputs Required: Source code directory path.
​Outputs Produced: Vulnerability report (PDF/Markdown).
​Tools Allowed: shell, read_file, skill/audit_crypto.
​Rules: Never share raw secrets in logs; always suggest a modern replacement for legacy crypto.
​Decision Policy: Assume "High" severity if unsure of an exploit's impact.
​Quality Checklist: [ ] Secrets redacted? [ ] NIST standards checked? [ ] Mitigation steps included?
​Agent 2: The Documentation Specialist
​Agent Name: DocuBot.
​Purpose: Convert complex code into readable documentation.
​Responsibilities: Generate README files, API references, and change logs.
​Out-of-Scope: Running code audits or performance testing.
​Inputs Required: Codebase and list of recent commits.
​Outputs Produced: Updated README.md and docs/ folder.
​Tools Allowed: read_file, write_file, skill/doc_summarize.
​Rules: Use technical but accessible language.
​Decision Policy: Ask for clarification if code comments are missing or ambiguous.
​Quality Checklist: [ ] Links functional? [ ] Examples included? [ ] Tone matches .claude.md?
​Task G: Multi-Agent Orchestration
​1. Orchestration Architecture
​1 Coordinator Agent: Receives the user request and routes it.
​Specialist 1 (Auditor): Checks for security risks.
​Specialist 2 (Doc Specialist): Updates the documentation based on the Auditor's findings.
​2. Policies
​Routing: If request includes "Check" or "Scan" → Specialist 1. If request includes "Explain" or "Write" → Specialist 2.  
​Conflict Resolution: If Specialist 1 finds a bug but Specialist 2 says the code is ready, the Coordinator halts the process until the bug is addressed.  
​Merge Strategy: The Coordinator combines the Audit Report and the Documentation into a single Final Report with a "Security First" header.  
​3. Orchestration Diagram

sequenceDiagram
    User->>Coordinator: Request Audit and Docs
    Coordinator->>Auditor: Scan /src for risks
    Auditor-->>Coordinator: Vulnerability Report
    Coordinator->>Doc Specialist: Document the findings
    Doc Specialist-->>Coordinator: README update
    Coordinator->>User: Final Package (Report + README)

