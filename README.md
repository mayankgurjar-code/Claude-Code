#Assignment: Claude Architecture & Agentic Workflows

​Task A: Architecture Write-up
​Claude-based workflows are structured as a layered ecosystem where reasoning is separated from execution.  
​The Layers of the Stack
​.claude.md (The Core Guidelines): This file acts as the "constitutional" layer. It defines global rules, reasoning habits, and safety boundaries that the model must always follow.  
​skill/ (The Action Library): A directory containing specialized procedures and tool "recipes". These allow Claude to handle specific tasks (like code audits) without needing to learn them from scratch every time.  
​Claude Engine (The Orchestrator): The central reasoning engine that interprets user intent. It plans the steps, selects the right tools, and validates that the output meets the user's needs.  
​Tools (The Functional Interface): These are the "hands" of the system, such as the shell, file system, or web browsers. They allow the model to interact with the real world.  
​User (The Director): The human who provides the intent, goals, and feedback.  
​Task B: Sample .claude.md (Global Instructions)
​This configuration is designed for a high-security engineering firm.  

# Engineering Standards: SecureOps AI

## 1. Tone and Style
- [span_18](start_span)Communication must be clinical, technical, and objective[span_18](end_span).
- [span_19](start_span)Avoid conversational filler; prioritize bulleted lists for clarity[span_19](end_span).

## 2. Reasoning Constraints
- **[span_20](start_span)Validation First:** Always generate a plan and ask for confirmation before modifying more than 3 files[span_20](end_span).
- **[span_21](start_span)Checklist Requirement:** Every complex task must start with a "Definition of Done" checklist[span_21](end_span).

## 3. Tool Usage & Safety
- **Shell Policy:** Only use the shell for `ls`, `cat`, and `grep`. [span_22](start_span)Never execute `rm` or `chmod` without explicit user permission[span_22](end_span).
- **[span_23](start_span)Data Privacy:** Automatically redact all environment variables and private keys from the output[span_23](end_span).

## 4. Output Format
- [span_24](start_span)All responses must end with a "Current System State" and "Action Items" section[span_24](end_span).

Task C: Skill Design (Library Modules)
​These skills represent modular capabilities.  
​1. skill/issue_analyzer
​Purpose: To parse bug reports and map them to specific code sections.  
​Inputs: A GitHub issue description and the source directory.  
​Tool Usage: Uses grep to find keywords and ls to map the file structure.  
​Test Case: Input a "404 Error" report → Expect identification of the routing logic in app.py.  
​2. skill/security_scan
​Purpose: To identify weak security configurations.  
​Outputs: A prioritized list of vulnerabilities with remediation steps.  
​Algorithm: Scans configuration files for default passwords or unencrypted ports.  
​3. skill/api_mapper
​Purpose: To generate a map of all available API endpoints from a codebase.  
​Inputs: Backend source files.  
​Steps: 1. Identify controller decorators. 2. Extract methods and paths. 3. Format as a table.  
​Task D: Claude Engine (Coordination)
​The engine manages the workflow through a series of internal loops:  
​Planning: Decomposes "Refactor the login logic" into sub-tasks.  
​Routing: Decides to call the skill/api_mapper to understand the current setup.  
​State Management: Tracks which files have been refactored to avoid redundant work.  
​Validation: Runs unit tests after changes to ensure stability.  
​Task F: Agent Design (Practical Designs)
​These agents follow the required template exactly.  
​Agent 1: The Code Guardian
​Agent Name: Guardian-Agent
​Purpose: Ensuring all code meets safety and style guidelines.  
​Responsibilities: Code review, linting check, and vulnerability detection.  
​Out-of-Scope: Fixing the code automatically; altering project infrastructure.  
​Tools Allowed: read_file, shell (for linting tools).  
​Rules: Never approve code that lacks docstrings or contains "TODO" comments.  
​Quality Checklist: [ ] No secrets found [ ] Linter passed [ ] Tests included.  
​Agent 2: The Documentation Architect
​Agent Name: Doc-Arch
​Purpose: Keeping technical documentation in sync with the code.  
​Responsibilities: Updating README files, generating API docs.  
​Out-of-Scope: Running the application; writing new feature code.  
​Inputs Required: Source code and recent commit history.  
​Tools Allowed: read_file, write_file.  
​Quality Checklist: [ ] All links active [ ] Table of contents updated.  
​Task G: Multi-Agent Orchestration
​This architecture uses a Coordinator-Specialist pattern.  
​Orchestration Components
​Coordinator Agent: Receives the user goal and breaks it into specialized tasks for the specialists.  
​Routing Policy: If the task requires deep code analysis → Route to Guardian-Agent. If it requires documentation → Route to Doc-Arch.  
​Conflict Resolution: If specialists provide conflicting information, the Coordinator reverts to the .claude.md rules to decide the final course of action.  
​Merge Strategy: The Coordinator combines the Guardan's security report and the Architect's documentation into one comprehensive summary.  
