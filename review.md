---
allowed-tools: Bash(*), Read, Glob, Grep, Edit
description: Review literate programming quality of .nw files in current branch
---

## Reference Files for Detailed Guidance

When reviewing, consult these reference files for detailed examples and patterns:

| Topic | Reference File |
|-------|----------------|
| Test organization | `~/.claude/skills/literate-programming/references/testing-patterns.md` |
| Noweb commands/syntax | `~/.claude/skills/literate-programming/references/noweb-commands.md` |
| Variation theory violations | `~/.claude/skills/variation-theory/references/common-violations.md` |
| LaTeX/Beamer patterns | `~/.claude/skills/didactic-notes/references/beamer-patterns.md` |
| Semantic environments | `~/.claude/skills/didactic-notes/references/semantic-environments.md` |

Read relevant reference files when you need detailed examples for a specific issue type.

## Current Context

- **Current branch:** !`git branch --show-current`
- **Default remote branch:** !`git rev-parse --abbrev-ref origin/HEAD 2>/dev/null`
- **All .nw files in repo:** !`git ls-files '*.nw'`
- **Recent commits on branch:** !`git log --oneline -5`

## Literate Programming Review Criteria

Review each changed `.nw` file against these criteria from the literate-programming skill:

### 1. Narrative Flow

- Does the document tell a coherent story?
- Is the order pedagogical (best for human understanding) rather than compiler-dictated?
- Does it start with the "why" before the "how"?

### 2. Variation Theory Application

- Are contrasts used to highlight key concepts (what IS vs what is NOT)?
- Does it follow "whole, then parts, then back together" structure?
- **CRITICAL**: Are concrete examples shown BEFORE stating general principles?
- Are anti-patterns shown alongside correct patterns where helpful?

**For detailed violation types with BAD/GOOD examples, read:** `~/.claude/skills/variation-theory/references/common-violations.md`

### 3. Chunk Quality

- **Names**: Do chunk names describe purpose (like pseudocode), not syntax?
  - Good: `<<validate user input>>`, `<<compute monthly totals>>`
  - Bad: `<<function definition>>`, `<<for loop>>`, `<<code>>`
- **Size**: Does each chunk represent a single, coherent concept?
- **Web structure**: Is the web structure used effectively (chunks defined out of order when helpful)?
- **Unused chunks**: Run `noroots *.nw` to detect typos in chunk names

### 4. Explanation Quality

- Does documentation explain "why" not just "what"?
- Are design decisions and trade-offs explained?
- Is technical context provided for non-obvious choices?
- Are code comments that explain logic moved to TeX documentation instead?

### 5. Test Organization (if tests present)

- **CRITICAL**: Do tests appear AFTER the functionality they verify?
- Are tests distributed throughout the file (not all grouped at beginning)?
- Is there pedagogical framing introducing each test section?
- Are tests within ~10 lines of their implementation?
- Do tests verify dependency assumptions (not just outcomes)?

**For detailed patterns and anti-patterns, read:** `~/.claude/skills/literate-programming/references/testing-patterns.md`

### 6. LaTeX Documentation Quality

Check for these common anti-patterns:

| Anti-pattern | Correct form |
|--------------|--------------|
| `\begin{itemize}` with `\item \textbf{Label}:` | `\begin{description}` with `\item[Label]` |
| `\texttt{some\_code}` with manual escaping | `[[some_code]]` notation |
| `"quoted text"` or ``` ``quoted'' ``` | `\enquote{quoted text}` |
| `Section~\ref{sec:foo}` | `\cref{sec:foo}` |
| LaTeX commands in Python docstrings | Plain text docstrings, LaTeX in prose |

**For Beamer mode splits and overlay patterns, read:** `~/.claude/skills/didactic-notes/references/beamer-patterns.md`
**For semantic environment selection, read:** `~/.claude/skills/didactic-notes/references/semantic-environments.md`

### 7. Noweb Syntax and Conventions

- Use `[[code]]` to quote code in documentation (escapes LaTeX specials)
- Use `<<[[filename.py]]>>` notation for filenames with underscores
- Check chunk definitions are properly formatted
- Verify no hardcoded magic numbers (use named constants)
- Constants belong in `<<constants>>` chunks, not `<<imports>>`

**For tangling/weaving commands and troubleshooting, read:** `~/.claude/skills/literate-programming/references/noweb-commands.md`

### 8. Progressive Disclosure

- Are abstract placeholder chunks used for high-level structure?
- Are implementation details deferred until pedagogically appropriate?
- For functions with 3+ related parameters, are they grouped conceptually?

## Your Task

1. **Identify files to review**: Use the changed .nw files from context above. If none found, ask the user which files to review.

2. **Read each .nw file** and analyze against ALL criteria above.

3. **Run `noroots`** on the .nw files to check for unused chunks (potential typos):
   ```bash
   noroots *.nw 2>/dev/null || echo "noroots not available"
   ```

4. **For each issue found**:
   - State the criterion violated
   - Show the file location (file:line)
   - Quote the problematic section
   - Explain why it's an issue
   - Propose a specific fix
   - **Ask the user**: "Would you like me to apply this fix? (yes/no/skip file)"

5. **Apply fixes** when user approves, using the Edit tool.

6. **After reviewing all files**, provide a summary:
   - Number of issues found per category
   - Number of fixes applied
   - Any remaining issues the user chose not to fix

## Review Priority

Focus on issues in this order (highest impact first):

1. **Test placement** - Tests before implementation is a major pedagogical problem
2. **Narrative flow** - Compiler-order vs pedagogical-order
3. **Chunk names** - Syntax-based names hurt readability
4. **Missing "why"** - Code without explanation
5. **LaTeX anti-patterns** - Style issues
6. **Unused chunks** - Potential bugs from typos

## Interactive Mode

When presenting issues, batch related issues in the same file together. For each batch:

```
## File: path/to/file.nw

### Issue 1: [Category] at line X
[Description and fix proposal]

### Issue 2: [Category] at line Y  
[Description and fix proposal]

Would you like me to:
1. Apply all fixes in this file
2. Apply fixes one by one (I'll ask for each)
3. Skip this file
4. Stop review
```

Respect the user's choice and proceed accordingly.
