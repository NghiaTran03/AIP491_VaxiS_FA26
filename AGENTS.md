# Agent Directive

## Environment & Toolchain (LaTeX)
- **Document Class & Engine:** Springer Nature template (`sn-jnl.cls`, `sn-mathphys-num.bst`), compiled with `pdflatex` + `bibtex` (or `latexmk -pdf main.tex`).
- **Primary Files:** `main.tex` (manuscript source), `references.bib` (BibTeX bibliography), `figures/` (graphic assets).
- **Build & Verification:** Compile changes via `pdflatex -interaction=nonstopmode main.tex` (and `bibtex main` when citations change) and inspect `main.log` to ensure zero LaTeX errors, undefined citations, or broken cross-references (`??`).

## Strict Execution Lifecycle & Transition Gates

Every modification task must strictly adhere to the sequential lifecycle below. Skipping or reordering steps is strictly prohibited:

1. **Phase 1: Implementation**
   - Apply LaTeX/BibTeX source edits according to the user's requirements and prompt constraints.
   - Verify that the document compiles cleanly without syntax errors, missing packages, or unresolved references.

2. **Phase 2: Code Review Gate (MANDATORY)**
   - **Trigger:** Immediately upon completing document modifications, BEFORE writing any walkthrough/summary.
   - **Action:** Delegate execution to the `code-reviewer` agent (defined in `.agents/agents/code-reviewer/agent.md`).
   - **Inspection Scope:**
     * Run `git status --short` and `git diff` (including `--cached` if staged).
     * Verify 100% diff alignment against the initial prompt to detect omissions, unintended changes, or scope creep.
     * Perform deep inspection for LaTeX compilation issues, broken labels/citations (`\ref`, `\cite`), formatting regressions, unescaped special characters (`%`, `_`, `&`, `#`), and stray draft/debug markup.
     * Verify clean PDF compilation (`pdflatex` / `bibtex`) within the reviewer context.
   - **Gate Rule:**
     * If verdict is **`[BLOCKED]`**: Halt forward progress immediately. Return to Phase 1 to patch the flagged issues, then re-trigger Phase 2.
     * Proceed to Phase 3 ONLY when verdict is explicitly marked **`[PASSED]`**.

3. **Phase 3: Summary & Walkthrough**
   - **Entry Condition:** Phase 2 is **`[PASSED]`**.
   - Synthesize changes, explain implementation details, and produce the final user-facing summary walkthrough.
   - **Commit Message Requirement:** Always accompany the summary walkthrough with a short, compact, one-line commit message for the changes (following conventional commits format, e.g. `docs(...)`, `fix(...)`, `chore(...)`).

### Non-Negotiable Directives
- **Zero-Bypass Policy:** Never generate summary artifacts or declare task completion without an explicit `[PASSED]` verdict from the `code-reviewer` agent.
- **No Duplicate Builds:** Never re-run LaTeX compilation after `code-reviewer` has already verified the build and returned `[PASSED]`.
- **Context Preservation:** When handing off to `code-reviewer`, pass the exact verbatim user prompt along with relevant context to serve as the ground truth.
- **Zero-Polling & Anti-Spam Policy for Subagents:**
  - Strictly prohibit polling in loops or spamming tools like `manage_subagents(Action="list")` and repetitive short `schedule(...)` timers (e.g., 5s–10s) while waiting for `code-reviewer` or other subagents.
  - Rely exclusively on the platform's reactive wakeup mechanism: after launching a subagent via `invoke_subagent`, immediately stop calling tools and wait for its completion message.

## Writing & Formatting Style
- Manuscript prose, LaTeX comments (`%`), and commit messages must be written in English (use `\vn{...}` or `\vntt{...}` macros for inline Vietnamese examples/terms where required).
- Do not use separator lines, banner dividers, or rows of equal signs in source files or chat outputs.
- Keep LaTeX markup, tables (`booktabs`), mathematical notation, terminal outputs, and chat responses clean and concise.
