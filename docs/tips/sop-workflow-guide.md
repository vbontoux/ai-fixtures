# SOP Workflow Guide: From Idea to Implementation

## Overview
This guide shows how to use three SOPs together to transform rough ideas into working code through a structured planning, design, and implementation workflow.

## The Three-Phase Workflow

### Comparison to DDD
Traditional Agile Hierarchy:
- Epic → Stories → Tasks → Subtasks

PDD Hierarchy:
- **Rough Idea** = Epic (high-level concept)
- **Detailed Design** = Epic specification (refined requirements + architecture)
- **Implementation Plan Steps** = Stories (each step is a demoable increment)
- **Code Tasks** (from code-task-generator) = Tasks (specific implementation work)

Key differences:
1. PDD focuses on design-first: You spend time in requirements clarification and research before breaking things down, whereas Agile often starts breaking down epics into stories earlier.

2. Implementation Plan Steps are like Stories: Each step in the PDD implementation plan (Step 1, Step 2, etc.) represents a story - it's a demoable increment of functionality that builds on previous work.

3. Code Tasks are generated from Steps: The code-task-generator SOP takes a single implementation plan step and breaks it into multiple code tasks (like breaking a story into tasks).

💡 We consider that the best level to start the PDD workflow is the EPIC level. But there is no restriction to start a PDD at a finer or coarser grain.


### Phase 0: setup your environment
#### Installations
In this document we are considering ```Kiro CLI``` but you can leverage your usual AI coding assistant.
* 👉 [Install Kiro CLI](https://kiro.dev/docs/cli/installation/)
  * For windows users take a look at [The essential guide to installing Kiro CLI on Windows](https://dev.to/aws/the-essential-guide-to-installing-amazon-q-developer-cli-on-windows-lmh)
* 👉 [Install Agent SOP](https://github.com/strands-agents/agent-sop/blob/main/README.md)
  * 💡 In Kiro CLI you can put the configuration into .kiro/agents/sop-agent.json. This way you can leverage the Kiro CLI command ```/agent swap``` to swap to the sop configuration.

#### Clear Context
👉 When you start any phase of this workflow, you must have a cleared context. Either you started a new Kiro CLI session or you should clear the context with the following command:    
```
/context clear
```

### Phase 1: Planning & Design (@pdd)
**Purpose:** Transform a rough idea into a detailed design with an implementation plan

**Input:** Rough idea (text, file, or URL)

**Process:**
1. Create project structure in `.sop/planning/` or your prefered location
2. Iteratively clarify requirements through Q&A (one question at a time)
3. Research relevant technologies, patterns, and existing code
4. Create detailed design document with architecture, components, and data models
5. Generate implementation plan with numbered steps and checklist

**Output:**
- `rough-idea.md` - Original concept
- `idea-honing.md` - Requirements Q&A
- `research/` - Research findings by topic
- `design/detailed-design.md` - Complete design specification
- `implementation/plan.md` - Step-by-step implementation plan with checklist

**Key Kiro CLI Command:**

1. Always start with a clean context.
[See Setup you environment](#clear-context)

2. Add exactly the relevent contents into the agent context  

👉 You must add you business initiative, Eprics with user stories, steering documents (...) into the agent's session context as markdown documents.

💡 If you have existing code that will serve as example, make sure it is accessible and your context documents reference the location of the code. Alternatively, you can take advantage or the [Codebase Summary SOP](https://github.com/strands-agents/agent-sop/blob/main/agent-sops/codebase-summary.sop.md)

Add the documents with the following command:
```
/context add [location of specs, steerings, ...]
```

Alway verify the context and adjust using:
```
/context show
```

1. Execute the PDD SOP

You must invoke the pdd prompt. You typically call the SOP MCP server "pdd" prompt but you can also invoke the prompt by providing the pdd prompt file.

```
@pdd
```

---

### Phase 2: Task Generation (@code-task-generator)
**Purpose:** Convert implementation plan steps into detailed, actionable code tasks

**Input:** 
- PDD implementation plan path, OR
- Direct task description

**Process:**
1. Detect input mode (PDD plan vs description)
2. For PDD: Find next uncompleted step from checklist
3. Break down step into logical implementation phases
4. Present task breakdown for approval
5. Generate structured `.code-task.md` files

**Output:**
- **PDD Mode:** `step{NN}/task-01-{title}.code-task.md`, `task-02-{title}.code-task.md`, etc.
- **Description Mode:** `{task-name}.code-task.md`

Each task includes:
- Description & background
- Reference documentation (design, research)
- Technical requirements
- Dependencies
- Implementation approach
- Acceptance criteria (Given-When-Then format)
- Metadata (complexity, skills, labels)

**Key Principle:** Break steps into functional components, NOT separate testing tasks. Tests are integrated into each implementation task.

---

### Phase 3: Implementation (@code-assist)
**Purpose:** Implement code tasks using test-driven development

**Input:** Task description or `.code-task.md` file path

**Process:**
1. **Setup:** Create documentation structure, discover CODEASSIST.md
2. **Explore:** Analyze requirements, research existing patterns, create context
3. **Plan:** Design test strategy, create implementation plan with checklist
4. **Code:**
   - Write tests first (TDD: RED)
   - Implement to pass tests (TDD: GREEN)
   - Refactor and align with codebase conventions (TDD: REFACTOR)
   - Validate: run tests and builds
5. **Commit:** Stage changes and commit with conventional commit message

**Output:**
- Working, tested code in `repo_root/`
- Documentation in `.sop/planning/implementation/{task_name}/`:
  - `context.md` - Requirements, patterns, dependencies
  - `plan.md` - Test scenarios and implementation strategy
  - `progress.md` - TDD cycles, checklist tracking
  - `logs/` - Build outputs

**Modes:**
- **interactive:** Confirm at each step, discuss approaches
- **auto:** Execute autonomously, document decisions

---

## Complete Workflow Example

```
1. Add context before starting
   → /context add [location of specs, steerings, ...]

2. Start with rough idea
   → Run @pdd
   → Get: design + implementation plan with 5 steps

3. Add context for implementation
   → /context add .sop/planning/**/*.md

4. Generate tasks for Step 1
   → Run @code-task-generator with plan path
   → Get: step01/task-01-*.md, task-02-*.md, task-03-*.md

5. Implement each task in sequence
   → Run @code-assist on task-01
   → Run @code-assist on task-02
   → Run @code-assist on task-03

6. After completing Step 1
   → Update plan.md checklist (mark Step 1 complete)
   → Optional: Start new session to reset context

7. Repeat steps 3-5 for remaining steps
```

---
