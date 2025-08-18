# Documentation Generation Workflow

## Phase 1: Requirements Collection

### Ask User Directly

Ask user questions. One by one.

1. Which project do you want to document? (Default: current project)
2. "Which documents do you need?" 
   - API Reference, User Guide, Architecture Overview, Security Documentation, Deployment Guide, Other (specify)

3. "Target audience?"
   - Developers, End users, Business stakeholders, Operations team,  Other (specify)

4. "Output documentation location?" (Default: `./docs`)

5. "Include diagrams?" (Yes/No)

6. "Document format?" (markdown/html)

## Phase 2: Requirements Confirmation

Print out parameters user entered for final confirmation.
If user agrees, continue to next Phase.
Otherwise, analyze the input and re-print for final confirmation.

## Phase 3: Workspace Setup

### Check the structure

Check if ./.ccdocs/{project-name}/ already exists. If yes, observe to find out what is already done and what is missing for required documents. Skip Create Structure if structure already exists.


### Create Structure
```bash
mkdir -p ./.ccdocs/{project-name}/{analysis,drafts,diagrams,agent-handoffs}
```

### Create Agent Configurations

For each required agent, create config file in `agent-handoffs/`:

**code-analyst-config.json**:
```json
{
  "project_path": "/absolute/path/to/project",
  "output_file": "./.ccdocs/{project}/analysis/code-analysis.md"
}
```

**technical-writer-config.json**:
```json
{
  "input_sources": ["./.ccdocs/{project}/analysis/"],
  "output_file": "./.ccdocs/{project}/drafts/{document-type}.md",
  "target_audience": "{audience}",
  "document_type": "{requested-document}"
}
```

**technical-writer-questions-config.json**:
```json
{
  "input_sources": ["./.ccdocs/{project}/analysis/"],
  "output_file": "./.ccdocs/{project}/analysis/writer-questions.md",
  "target_audience": "{audience}",
  "document_type": "{requested-document}",
  "task_mode": "question_generation"
}
```

**critical-reader-analysis-config.json**:
```json
{
  "input_file": "./.ccdocs/{project}/analysis/code-analysis.md",
  "output_file": "./.ccdocs/{project}/analysis/analysis-review-report.md",
  "focus_areas": ["accuracy", "completeness", "technical_correctness", "gap_identification"],
  "validation_type": "analysis_review"
}
```

**critical-reader-validation-config.json**:
```json
{
  "input_file": "./.ccdocs/{project}/analysis/code-analysis.md", 
  "previous_issues_file": "./.ccdocs/{project}/analysis/analysis-review-report.md",
  "output_file": "./.ccdocs/{project}/analysis/validation-report.md",
  "validation_type": "correction_verification"
}
```

**critical-reader-document-config.json**:
```json
{
  "input_file": "./.ccdocs/{project}/drafts/{document-type}.md",
  "output_file": "./.ccdocs/{project}/analysis/document-review-report.md",
  "focus_areas": ["quality", "accuracy", "completeness", "target_audience_fit"],
  "validation_type": "document_review"
}
```

**api-specialist-config.json**:
```json
{
  "project_path": "/absolute/path/to/project",
  "input_analysis": "./.ccdocs/{project}/analysis/code-analysis.md",
  "output_file": "./.ccdocs/{project}/analysis/api-analysis.md",
  "focus_areas": ["endpoints", "authentication", "data_models", "integration_patterns"]
}
```

**security-reviewer-config.json**:
```json
{
  "project_path": "/absolute/path/to/project", 
  "input_analysis": "./.ccdocs/{project}/analysis/code-analysis.md",
  "output_file": "./.ccdocs/{project}/analysis/security-analysis.md",
  "focus_areas": ["authentication", "authorization", "data_protection", "vulnerabilities", "compliance"]
}
```

**critical-reader-diagram-config.json** (for diagram validation):
```json
{
  "input_location": "{output-location}",
  "focus_areas": ["diagram_placement", "visual_quality", "content_accuracy", "integration", "completeness"],
  "output_file": "./.ccdocs/{project}/analysis/diagram-review-report.md",
  "validation_type": "diagram_integration"
}
```

**plantuml-diagrammer-config.json**:
```json
{
  "input_location": "{output-location}",
  "assets_folder": "{output-location}/assets",
  "analysis_mode": "intelligent_enhancement",
  "create_missing_diagrams": true,
  "convert_ascii_diagrams": true,
  "diagram_types": ["component", "sequence", "deployment", "network"],
  "output_format": "svg"
}
```

## Phase 4: Agent Selection

### Universal Agent Flow

All document types follow the same comprehensive flow with specialized analysis based on content requirements:

**Core Analysis Flow**:
1. **code-analyst** → provides foundational codebase analysis
2. **Parallel Specialist Analysis** (as needed):
   - **api-specialist** → if APIs/endpoints detected
   - **security-reviewer** → if security aspects relevant
3. **Iterative Writing Cycle**:
   - **technical-writer** (question generation) + **critical-reader** (analysis review) → parallel
   - **code-analyst** (address questions/issues)
   - **critical-reader** (validate corrections)
   - **technical-writer** (final document creation)
4. **Visual Enhancement** (if diagrams requested):
   - **plantuml-diagrammer** → create/enhance diagrams
   - **critical-reader** (diagram validation)

### Dynamic Specialist Selection

Based on project analysis, automatically include relevant specialists:

**API-focused content**: Include **api-specialist** if:
- REST endpoints found in code
- GraphQL schemas detected  
- API documentation requested
- Microservices architecture identified

**Security-focused content**: Include **security-reviewer** if:
- Authentication/authorization code found
- Security documentation requested
- External integrations detected
- Compliance requirements mentioned

**Architecture-focused content**: Always include **plantuml-diagrammer** if diagrams requested (regardless of document type)

## Phase 5: Execute Agents

### Step 1: Initial Analysis Phase

**Launch code-analyst for foundational analysis:**
```
Task(
  subagent_type: "code-analyst",
  prompt: "Task: Analyze project codebase for {document-types} documentation.

  CRITICAL: Base analysis ONLY on actual source code, configuration files, and build scripts. 
  DO NOT use README.md, existing documentation, or comments as sources of truth.
  Any existing documentation can only be referenced AFTER verifying it matches actual code behavior.

  Instructions:
  1. Examine actual source code structure and implementation
  2. Analyze configuration files, package.json, build scripts
  3. Identify actual functionality through code examination
  4. Document what the code actually does, not what documentation claims
  5. If referencing existing docs, verify claims against actual code first

  Your configuration: ./.ccdocs/{project}/agent-handoffs/code-analyst-config.json"
)
```

**Validation:**
- ✅ Check `./.ccdocs/{project}/analysis/code-analysis.md` exists
- ✅ File contains comprehensive codebase analysis
- ❌ If validation fails: re-launch with explicit output instruction

### Step 2: Specialist Analysis (Parallel, if needed)

**Launch specialists based on project characteristics:**

**If APIs detected:**
```
Task(
  subagent_type: "api-specialist",
  prompt: "Task: Analyze APIs based on code-analyst findings.

  CRITICAL: Base analysis ONLY on actual API code, route definitions, and implementation files.
  DO NOT use API documentation or comments as sources of truth.
  Verify any documented API behavior against actual code implementation.

  Instructions:
  1. Read code-analyst findings: ./.ccdocs/{project}/analysis/code-analysis.md
  2. Examine actual API endpoint implementations
  3. Analyze route definitions, middleware, request/response handling
  4. Document actual API behavior as implemented in code
  5. Verify any claims against running code or implementation details

  Your configuration: ./.ccdocs/{project}/agent-handoffs/api-specialist-config.json"
)
```

**If security aspects relevant:**
```
Task(
  subagent_type: "security-reviewer", 
  prompt: "Task: Analyze security aspects based on code-analyst findings.

  CRITICAL: Base analysis ONLY on actual security implementation in code.
  DO NOT rely on security documentation or comments as sources of truth.
  Examine actual authentication, authorization, and security code implementation.

  Instructions:
  1. Read code-analyst findings: ./.ccdocs/{project}/analysis/code-analysis.md
  2. Examine actual security implementation (auth, encryption, validation)
  3. Analyze access control, data protection, and vulnerability patterns
  4. Document actual security measures as implemented in code
  5. Identify security gaps based on code analysis, not documentation claims

  Your configuration: ./.ccdocs/{project}/agent-handoffs/security-reviewer-config.json"
)
```

### Step 3: Iterative Analysis Refinement Cycle

**3.1. Parallel Question Generation & Analysis Review:**

**IMPORTANT: Execute both Task calls in single message for parallel processing**

Launch **BOTH** agents simultaneously:

```
// Execute both simultaneously in one message:
Task(subagent_type: "technical-writer", prompt: "...")
Task(subagent_type: "critical-reader", prompt: "...")
```

**technical-writer (for questions):**
```
Task(
  subagent_type: "technical-writer",
  prompt: "Task: Generate specific questions for code-analyst to improve analysis.

  Context: You need to create {document-types} for {audience} audience. 
  Review the current analysis and identify what additional information you need from code-analyst to write complete, accurate documentation.

  Instructions:
  1. Read the analysis files in ./.ccdocs/{project}/analysis/
  2. Identify gaps and missing information needed for {document-types}
  3. Generate specific, actionable questions for code-analyst
  4. Focus on information gaps that prevent quality documentation creation
  5. Output questions to file specified in your configuration
  
  Your configuration: ./.ccdocs/{project}/agent-handoffs/technical-writer-questions-config.json"
)
```

**critical-reader (for analysis review):**
```
Task(
  subagent_type: "critical-reader", 
  prompt: "Task: Review and validate code-analyst output quality.

  CRITICAL: Verify all claims against actual source code, NOT against existing documentation.
  Existing documentation may be outdated or incorrect - validate against actual code implementation.

  Instructions:
  1. Read code-analyst output: ./.ccdocs/{project}/analysis/code-analysis.md
  2. Verify EVERY claim against actual project code structure and implementation
  3. Check that analysis is based on code examination, not documentation assumptions
  4. Identify any reliance on README/docs without code verification
  5. Flag issues requiring correction before proceeding to documentation writing
  6. Ensure completeness for {document-types} requirements based on actual code capabilities
  
  Focus areas: code-based accuracy, implementation verification, factual correctness
  Your configuration: ./.ccdocs/{project}/agent-handoffs/critical-reader-analysis-config.json"
)
```

**3.2. Analysis Improvement Cycle:**

**Launch code-analyst with feedback:**
```
Task(
  subagent_type: "code-analyst",
  prompt: "Task: Improve your analysis based on feedback and questions.

  Context: Your previous analysis has been reviewed. You must address specific issues and answer questions to improve the analysis quality.

  Instructions:
  1. Read your previous analysis: ./.ccdocs/{project}/analysis/code-analysis.md
  2. Read critical-reader issues: ./.ccdocs/{project}/analysis/analysis-review-report.md
  3. Read technical-writer questions: ./.ccdocs/{project}/analysis/writer-questions.md
  4. Address EACH specific issue identified by critical-reader
  5. Answer EACH question from technical-writer with detailed analysis
  6. Update your analysis file with corrections and additional information
  7. Ensure all identified gaps are filled with accurate, verifiable information

  CRITICAL REQUIREMENTS: 
  - Base ALL updates on actual code examination, NOT on existing documentation
  - Verify every claim against source code implementation
  - Do not trust README/docs without code verification
  - Document what the code actually does, not what documentation claims it does

  Your configuration: ./.ccdocs/{project}/agent-handoffs/code-analyst-config.json"
)
```

**3.3. Validation of Improvements:**

**Launch critical-reader to verify corrections:**
```
Task(
  subagent_type: "critical-reader",
  prompt: "Task: Verify that code-analyst has addressed all issues and questions.

  Instructions:
  1. Read your previous review: ./.ccdocs/{project}/analysis/analysis-review-report.md
  2. Read technical-writer questions: ./.ccdocs/{project}/analysis/writer-questions.md
  3. Read updated analysis: ./.ccdocs/{project}/analysis/code-analysis.md
  4. Verify EACH issue you identified has been properly addressed
  5. Verify EACH technical-writer question has been answered adequately
  6. Check that new information is accurate and based on actual code (NOT documentation)
  7. Verify no reliance on README/docs without code verification
  8. Identify any remaining issues that require further correction
  9. Approve analysis only if ALL issues are resolved and based on code examination

  CRITICAL: Only approve if analysis is factually accurate based on actual code implementation.
  Decision: APPROVE (ready for documentation) or REQUIRES_CORRECTION (specify remaining issues)
  Your configuration: ./.ccdocs/{project}/agent-handoffs/critical-reader-validation-config.json"  
)
```

**3.4. Repeat if needed:**
- If critical-reader still has blocking issues → repeat steps 3.2-3.3
- Maximum 2 correction cycles to prevent endless loops
- Continue only when critical-reader approves the analysis

### Step 4: Document Creation

**Launch technical-writer for final documentation:**
```
Task(
  subagent_type: "technical-writer",
  prompt: "Task: Create {document-types} documentation based on validated analysis.

  Context: The analysis has been validated and approved by critical-reader. All your questions have been answered.

  Instructions:
  1. Read validated analysis: ./.ccdocs/{project}/analysis/code-analysis.md
  2. Read any specialist analyses: ./.ccdocs/{project}/analysis/ (api-specialist, security-reviewer outputs)
  3. Create comprehensive {document-types} documentation for {audience} audience
  4. Use the validated analysis as your authoritative source
  5. Structure content appropriately for the document type
  6. Include specific examples and code references where relevant
  7. Output to location specified in your configuration

  Quality requirements: Clear, accurate, complete documentation that serves the target audience
  Your configuration: ./.ccdocs/{project}/agent-handoffs/technical-writer-config.json"
)
```

**Document validation by critical-reader:**
```
Task(
  subagent_type: "critical-reader",
  prompt: "Task: Review and validate created documentation quality.

  Instructions:
  1. Read the created documentation: ./.ccdocs/{project}/drafts/{document-type}.md
  2. Compare against the validated analysis for accuracy
  3. Verify completeness for {document-types} requirements
  4. Check that content serves {audience} audience appropriately
  5. Identify any errors, omissions, or clarity issues
  6. Ensure documentation structure and flow are logical
  7. Verify all claims are supported by the analysis

  Validation criteria: quality, accuracy, completeness, target audience fit
  Decision: APPROVE (ready for publication) or REQUIRES_REVISION (specify issues)
  Your configuration: ./.ccdocs/{project}/agent-handoffs/critical-reader-document-config.json"
)
```

**Document correction cycle (if needed):**
- If critical-reader identifies issues → re-launch technical-writer with feedback
- Maximum 2 correction cycles
- Continue only when critical-reader approves the documentation

### Validation Protocol for Each Agent
After each agent execution:
- ✅ Output file exists at specified path
- ✅ File contains expected content structure  
- ✅ No error messages in agent response
- ✅ critical-reader approval (where applicable)
- ❌ If any validation fails: implement correction cycle

## Phase 6: Final Assembly

### Copy to Output Location
1. Read final document from `./.ccdocs/{project}/drafts/{document-type}.md`
2. Copy to user-specified output location (default: `./docs/`)
3. Create `assets/` subdirectory in output location

## Phase 7: Visual Enhancement (if diagrams requested)
**Important**: Only if user requested diagrams in Phase 1:

### Step 1: Diagram Generation
Launch `plantuml-diagrammer` agent:

```
Task(
  subagent_type: "plantuml-diagrammer",
  prompt: "Analyze documentation at [output-location] and enhance with diagrams. 
  Target audience: {audience}. 
  Document type: {document-type}.
  Configuration: ./.ccdocs/{project}/agent-handoffs/plantuml-diagrammer-config.json"
)
```

### Step 2: Diagram Validation by critical-reader
Launch `critical-reader` to validate diagram integration:

```
Task(
  subagent_type: "critical-reader",
  prompt: "Review documentation at [output-location] for diagram integration quality.
  Focus: diagram placement, visual quality, content accuracy, integration, completeness.
  Configuration: ./.ccdocs/{project}/agent-handoffs/critical-reader-diagram-config.json"
)
```

### Step 3: Diagram Correction Cycle (if needed)
If critical-reader identifies issues requiring correction:

1. **Re-launch plantuml-diagrammer** with specific feedback:
```
Task(
  subagent_type: "plantuml-diagrammer", 
  prompt: "Address critical-reader issues: {specific_feedback}. 
  Update diagrams and documentation integration.
  Configuration: ./.ccdocs/{project}/agent-handoffs/plantuml-diagrammer-config.json"
)
```

2. **Re-launch critical-reader** to validate corrections:
```
Task(
  subagent_type: "critical-reader",
  prompt: "Verify that diagram issues have been resolved.
  Configuration: ./.ccdocs/{project}/agent-handoffs/critical-reader-diagram-config.json"
)
```

3. **Repeat cycle if needed** (maximum 2 correction cycles)
4. **Continue only when critical-reader approves diagrams**

### Step 4: Final Diagram Verification
- ✅ SVG files exist in `[output-location]/assets/`
- ✅ Markdown files updated with proper `![name](./assets/name.svg)` references
- ✅ No ASCII diagrams remain (converted to SVG)
- ✅ All complex concepts have supporting diagrams
- ✅ critical-reader approval of diagram quality and integration
- ✅ No placeholder markers or broken references remain

## Phase 8: Completion Report
Report to user:
- ✅ **Documents created**: [list of documents]
- 📊 **Diagrams generated**: [number] diagrams created (if applicable)
  - ASCII conversions: [number] converted to SVG
  - New diagrams added: [number] created for complex concepts
  - Diagram types: [component, sequence, deployment, etc.]
- 📁 **Output location**: [path]
- 🔍 **Quality reviews completed**: 
  - Content review: [passed/issues found and resolved]
  - Diagram validation: [passed/correction cycles needed] (if applicable)
- ⏱️ **Total time**: [minutes]
- 🎯 **Enhancement summary**: [brief description of visual improvements made]

## Error Recovery

### Agent Failed to Create Output
1. Re-launch agent with explicit output file instruction
2. If still fails: Skip agent, note in final report

### Configuration File Missing
1. Recreate config file with correct paths
2. Re-launch affected agent

### Invalid Output Content  
1. Launch critical-reader to identify issues
2. Re-launch original agent with issue feedback

### Critical-Reader Identifies Analysis Errors
If critical-reader identifies factual errors in the analysis phase:
1. **Re-launch code-analyst** to correct the analysis:
```
Task(
  subagent_type: "code-analyst", 
  prompt: "Review and correct your analysis based on critical-reader feedback. Your configuration: ./.ccdocs/{project}/agent-handoffs/code-analyst-config.json"
)
```
2. **Verify corrected analysis** before proceeding with other agents
3. **Re-launch technical-writer** only after analysis is corrected