# Escalation Protocol

When a developer agent reports a blocker, use this framework to decide: handle it yourself, or escalate to the user.

## Decision Matrix

### Handle Yourself (High Confidence)

Decide without involving the user when:
- The answer is explicitly stated in the plan (developer missed it)
- It's a standard engineering convention (naming, file structure, error handling patterns)
- The developer is asking about implementation order and the dependency graph makes it clear
- The choice doesn't affect user-facing behavior or architecture
- Both options are roughly equivalent and won't surprise the user

**Examples:**
- "Should this be sync or async?" → Plan says async, or async is the obvious choice for I/O
- "What naming convention for files?" → Follow the project's existing convention
- "Should I add input validation?" → Yes, at system boundaries, always
- "Which test framework?" → Use whatever the project already uses

**When you decide:** Log the decision in your execution summary. Note what was asked and why you chose what you chose.

### Escalate to User (Low Confidence)

Involve the user when:
- The decision affects architecture, user experience, or external dependencies
- There's a meaningful trade-off between approaches (not just cosmetic)
- The developer found something that contradicts or extends the plan
- You're unsure, even slightly — the cost of asking is low
- The choice involves third-party services, libraries, or APIs not in the plan
- The decision could set a precedent for other tasks

**Examples:**
- "I found a library that does 80% of this. Use it or build custom?"
- "The plan says X, but I think Y would be better because..."
- "This task might conflict with TASK-005's scope"
- "The acceptance criteria are ambiguous about edge case Z"

**When you escalate:** Present to the user:
1. **What:** What the developer is asking
2. **Why it matters:** Impact of getting this wrong
3. **Your read:** What you think the right call is (or that you genuinely don't know)
4. **Options:** The choices available, with brief pros/cons

Then relay the user's decision back to the developer by spawning a follow-up Agent for the same task with the clarification included in the prompt.

## Default Bias

When in doubt, **escalate**. A 30-second user decision is cheaper than an hour of rework. The user explicitly chose to stay in the loop for design decisions — respect that.
