# Orchestrator Cleanup Plan

## Minimal Changes Principle
Make ONLY the explicitly listed changes below. Do NOT:
- Rewrite, reformat, or restructure surrounding content
- Change wording of unchanged sections
- Add or remove blank lines beyond what the edit requires
- Reorder, consolidate, or improve anything not in scope

Every edit must be the smallest possible diff. When in doubt, leave it unchanged.

---

## Part A — vscode-agents/orchestrator.agent.md

### A1. Restrict tools list (front-matter)
`tools: ['vscode', 'execute', 'read', 'agent', 'context7/*', 'github/*', 'edit', 'search', 'web', 'memory', 'todo']`
→ `tools: ['read', 'agent', 'vscode/memory']`

### A2. Strengthen Step 1 (Get the Plan)
Append two paragraphs after the existing "The Planner will return implementation steps." sentence, before `### Step 2`:

> **IMPORTANT:** The Planner's output MUST include detailed implementation steps with **explicit file assignments per step** (which files each step creates/modifies). This detail is required for the phase gate review (Step 3) and for parallelization parsing (Step 2). If the Planner returns a plan without file assignments, send it back to the Planner with instructions to add them.
>
> The Planner will save the implementation plan to `output/plans/{short-description}-plan.md` and return its contents.

### A3. Add "proceed immediately" marker to Step 2
After the closing ` ``` ` of the execution plan example block in Step 2, append:

> Proceed immediately to Step 3 — do NOT stop or ask for confirmation here.

### A4. Insert new Step 3: Human Review Gate (MANDATORY STOP)
Insert between Step 2 and the current "Step 3: Execute Each Phase". Full text:

```
### Step 3: Human Review Gate (MANDATORY STOP)
Present the FULL detailed plan from the Planner (Step 1) AND the parsed execution phases (Step 2) together in chat, then display this phase gate message:

```
⏸️ PHASE GATE — Plan & Execution Review

The implementation plan has been saved to: output/plans/{short-description}-plan.md

[Present the full plan content here — summary, scope, implementation steps with file assignments, edge cases, open questions]

## Execution Phases

### Phase 1: [Name]
- Task 1.1: [description] → [Agent]
  Files: [file list]
- Task 1.2: [description] → [Agent]
  Files: [file list]
(No file overlap → PARALLEL)

### Phase 2: [Name] (depends on Phase 1)
- Task 2.1: [description] → [Agent]
  Files: [file list]

Please review the plan and execution phases, then respond with one of:
- "Approved" — proceed to execution
- Feedback/changes — I'll revise the plan and re-present for review
```

**STOP and WAIT for user response. Do NOT proceed to execution.**

- If user provides feedback: re-run the **Planner** agent with the feedback to revise the plan and update the plan file, then re-parse execution phases (Step 2), and re-present for review (Step 3)
- If user approves: proceed directly to Step 4 (Execute Each Phase). **Do NOT ask for additional confirmation — approval at this gate is the only approval needed.**
```

### A5. Renumber existing steps
- `### Step 3: Execute Each Phase` → `### Step 4: Execute Each Phase`
- `### Step 4: Review Before Finalizing` → `### Step 5: Review Before Finalizing`
- `### Step 5: Report Results` → `### Step 6: Report Results`

---

## Part B — All 14 sub-agents

For each sub-agent file, apply ONLY these targeted changes. Touch nothing else.

### B1. Add `[Orch] ` prefix to `name:` in every sub-agent
| File | Old name | New name |
|------|----------|----------|
| clarifier.agent.md | Clarifier | [Orch] Clarifier |
| planner.agent.md | Planner | [Orch] Planner |
| junior-dev.agent.md | Junior Developer | [Orch] Junior Developer |
| frontend-dev.agent.md | Frontend Developer | [Orch] Frontend Developer |
| backend-dev.agent.md | Backend Developer | [Orch] Backend Developer |
| fullstack-dev.agent.md | Fullstack Developer | [Orch] Fullstack Developer |
| sr-frontend-dev.agent.md | Senior Frontend Developer | [Orch] Senior Frontend Developer |
| sr-backend-dev.agent.md | Senior Backend Developer | [Orch] Senior Backend Developer |
| sr-fullstack-dev.agent.md | Senior Fullstack Developer | [Orch] Senior Fullstack Developer |
| data-engineer.agent.md | Data Engineer | [Orch] Data Engineer |
| designer.agent.md | Designer | [Orch] Designer |
| prompt-writer.agent.md | Prompt Writer | [Orch] Prompt Writer |
| devops.agent.md | DevOps | [Orch] DevOps |
| reviewer.agent.md | Reviewer | [Orch] Reviewer |

### B2. Remove `'context7/*'` from tools list in every sub-agent
### B3. Remove `'github/*'` from tools list where present
Files with `github/*`: junior-dev, frontend-dev, backend-dev, fullstack-dev, sr-frontend-dev, sr-backend-dev, sr-fullstack-dev, data-engineer, prompt-writer

### B4. Change `'memory'` → `'vscode/memory'` in every sub-agent tools list

### B5. Remove body references to #context7

**8 dev agents** (junior-dev, frontend-dev, backend-dev, fullstack-dev, sr-frontend-dev, sr-backend-dev, sr-fullstack-dev, data-engineer):
- Delete the opening paragraph: "ALWAYS use #context7 MCP Server to read relevant documentation. Do this every time you are working with a language, framework, library etc. Never assume that you know the answer as these things change frequently. Your training date is in the past so your knowledge is likely out of date, even if it is a technology you are familiar with."

**planner.agent.md**:
- `2. **Verify**: Use #context7 and #fetch to check documentation` → `2. **Verify**: Use #fetch to check documentation`

**devops.agent.md**:
- Opening body: remove "using #context7 before executing," → "before executing,"
- `6. Search documentation using #context7` → `6. Search official documentation`
- `2. **Use Context7**: Verify commands and best practices for libraries/frameworks` → `2. **Search Docs**: Verify commands and best practices for libraries/frameworks`

**reviewer.agent.md**:
- `Use #context7 to check current best practices for libraries/frameworks` → `Use web search to check current best practices for libraries/frameworks`
- `5. **Be Current**: Use #context7 to verify best practices haven't changed` → `5. **Be Current**: Use web search to verify best practices haven't changed`

---

## Verification
```bash
grep "name:" vscode-agents/*.agent.md          # all subs show [Orch] prefix; orchestrator does not
grep -r "context7\|github/\*" vscode-agents/   # zero matches
grep "'memory'" vscode-agents/*.agent.md        # zero bare 'memory' (only 'vscode/memory')
```
