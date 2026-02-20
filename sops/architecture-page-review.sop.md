# Architecture Page Review

## Overview

This SOP helps architects review Architecture Principle (AP) pages and their linked articles to verify accuracy, consistency, and completeness. It analyzes markdown documentation, extracts key information, identifies inconsistencies, and proposes improvements for MUST/SHOULD/MAY statements.

## Parameters

- **ap_page_path** (required): Path to the Architecture Principle page to review
- **depth_level** (optional, default: 1): Number of levels of linked articles/sub-pages to analyze
- **output_directory** (optional, default: "./review-output"): Directory where review files will be saved

**Constraints for parameter acquisition:**
- You MUST ask for all required parameters upfront in a single prompt
- You MUST support multiple input methods for ap_page_path including direct path, relative path, or file selection
- You MUST validate that the AP page exists before proceeding
- You MUST create the output directory if it doesn't exist

## Steps

### 1. Read and Parse AP Page

Read the specified Architecture Principle page and extract its core metadata.

**Constraints:**
- You MUST extract the following from the maturity_level placeholder: name, ID, statement, and rationale
- You MUST identify all MUST/SHOULD/MAY statements with their unique IDs from notice placeholders
- You MUST extract all links to related articles and sub-pages
- You MUST save the extracted metadata to a temporary working file for reference

### 2. Analyze Linked Articles

Recursively analyze linked articles and sub-pages up to the specified depth level.

**Constraints:**
- You MUST follow links up to the depth_level specified
- You MUST extract MUST/SHOULD/MAY statements from each linked page
- You MUST track the relationship between pages (parent-child structure)
- You MUST identify circular references to avoid infinite loops because this could cause the analysis to hang indefinitely
- You SHOULD note any broken links or missing pages

### 3. Generate Summary File

Create a comprehensive summary of the AP page and all analyzed linked content.

**Constraints:**
- You MUST save the summary to `{output_directory}/{AP-ID}-summary.md`
- You MUST include the following sections in the summary:
  - Architecture Principle Overview (name, ID, statement, rationale)
  - Key Definitions (extracted from all pages)
  - Standards Referenced (tools, frameworks, technologies mentioned)
  - Policy Statements (all MUST statements with IDs)
  - Guidelines (all SHOULD/MAY statements with IDs)
  - Recommended Reviewers (roles/specialties based on content analysis)
- You MUST organize content hierarchically showing the page structure
- You SHOULD highlight critical requirements that need expert review

### 4. Generate Inconsistencies File

Identify and document all inconsistencies found across the analyzed pages.

**Constraints:**
- You MUST save inconsistencies to `{output_directory}/{AP-ID}-inconsistencies.md`
- You MUST check for the following types of inconsistencies:
  - Conflicting MUST/SHOULD/MAY statements
  - Duplicate statement IDs
  - Contradictory definitions of the same term
  - Broken or circular references
  - Missing rationale for requirements
  - Inconsistent terminology usage
- You MUST provide the location (file and section) for each inconsistency
- You MUST categorize inconsistencies by severity (Critical, High, Medium, Low)
- You SHOULD suggest potential resolutions for each inconsistency

### 5. Generate Recommendations File

Propose additional MUST/SHOULD/MAY statements that should be added to strengthen the architecture documentation.

**Constraints:**
- You MUST save recommendations to `{output_directory}/{AP-ID}-recommendations.md`
- You MUST base recommendations on:
  - Gaps in current coverage
  - Industry best practices for the topic area
  - Logical extensions of existing statements
  - Common implementation requirements not explicitly stated
- You MUST provide for each recommendation:
  - Proposed statement text with appropriate RFC2119 keyword
  - Suggested unique ID following the existing naming pattern
  - Target page and section where it should be inserted
  - Rationale for the addition
- You SHOULD prioritize recommendations by importance

### 6. Generate Review Plan

Create an actionable plan to address findings and coordinate specialist reviews.

**Constraints:**
- You MUST save the plan to `{output_directory}/{AP-ID}-review-plan.md`
- You MUST include the following sections:
  - Executive Summary (high-level findings)
  - Critical Issues (inconsistencies requiring immediate attention)
  - Specialist Review Assignments (which roles should review which sections)
  - Definition Standardization Tasks (terms needing clarification)
  - Recommended Additions (prioritized list from recommendations file)
  - Timeline Suggestion (proposed order of activities)
- You MUST identify specific roles/specialties needed for review based on content
- You SHOULD group related tasks for efficiency

### 7. Generate Email Message

Create an email message to send to specialists for review coordination.

**Constraints:**
- You MUST save the email to `{output_directory}/{AP-ID}-email.md`
- You MUST include in the email:
  - Subject line with AP ID and name
  - Brief overview of the architecture principle
  - Summary of key findings (inconsistencies count, recommendations count)
  - List of specialist roles needed and their assigned sections
  - Links or paths to the review files (summary, inconsistencies, recommendations, review plan)
  - Clear call to action with expected timeline
- You MUST format the email in a professional, concise manner
- You SHOULD highlight critical issues requiring immediate attention

### 8. Present Results

Display a summary of the review process and guide the user to the generated files.

**Constraints:**
- You MUST confirm that all five files were successfully created
- You MUST display the file paths for easy access
- You MUST provide a brief summary of key findings including:
  - Total pages analyzed
  - Number of MUST/SHOULD/MAY statements found
  - Number of inconsistencies by severity
  - Number of recommendations proposed
- You SHOULD highlight the most critical issues requiring immediate attention

## Examples

### Example Input
```
ap_page_path: /docs/architecture/principles/AP-08-observability.md
depth_level: 2
output_directory: ./reviews/AP-08
```

### Example Output Structure

**AP-08-summary.md:**
```markdown
# Architecture Principle Review Summary: Observability (AP-08)

## Overview
- **ID:** AP-08
- **Name:** Observability
- **Statement:** SBS Application Components must be easily operable by Institutions or SBS operators.

## Key Definitions
- **Structured Logging:** ...
- **Observability:** ...

## Standards Referenced
- OpenTelemetry
- Prometheus
- Grafana

## Policy Statements (MUST)
1. AP-08.OBS.R1: All Application Components MUST provide structured logging capabilities
2. AP-08.OBS.R2: ...

## Recommended Reviewers
- DevOps Architect
- Site Reliability Engineer
- Security Specialist
```

## Troubleshooting

### Broken Links in Documentation
If the SOP encounters broken links, it will note them in the inconsistencies file but continue processing other valid links. Review the inconsistencies file to identify and fix broken references.

### Circular References
If circular references are detected between pages, the SOP will stop following that path at the depth where the cycle is detected to prevent infinite loops. This will be noted in the summary file.

### Missing Statement IDs
If MUST/SHOULD/MAY statements are found without unique IDs in notice placeholders, they will be flagged in the inconsistencies file as requiring ID assignment.
