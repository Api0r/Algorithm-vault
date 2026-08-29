# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Vault Overview

This is a C++ competitive programming (算法竞赛) knowledge vault for OI/ACM/Codeforces/牛客 training.

## Language & Code Conventions

- **Primary language**: C++ (C++17/20)
- **Code style**: `#define int long long`, `#define endl '\n'`, `ios::sync_with_stdio(false); cin.tie(nullptr);`
- **Template pattern**: `signed main() { ... }` with `int T_cases` loop calling `solve()`
- **Data structures**: handwritten (dsu, segment tree, BIT, etc.) — rarely use STL containers beyond vector/string
- **Build**: single-file compilation with `g++ -std=c++17 -O2` — no build system, no tests
- **Template writing style** (follow this structure for data-structure templates):
  - **Documentation header**: a `/* ... */` comment at the top of the struct explaining purpose, all public interfaces (1-indexed), and edge-case semantics (e.g. "非法 k 返回 n + 1")
  - **Default template param**: `template <class T = int>` (or `= long long` for large values) so the common case needs no explicit type
  - **Constructors**: `T() = default;` + `explicit T(args) { init(args); }` delegating to `init`
  - **`init` method**: sets size fields and `assign`s internal arrays to `T{}` (zero-init)
  - **Overloaded methods**: same name for related operations with different arity (e.g. `add(x, y, v)` single-point vs `add(a, b, c, d, v)` rectangle)
  - **Early returns**: guard invalid ranges (`if (l > r) return;`, `if (a > c || b > d) return;`)
  - **`std::` qualification**: use `std::vector`, `std::min`, `static_cast<T>` instead of raw C-style casts
  - **Boundary clamping**: clamp queries (`x = std::min(x, n)`) and guard `<= 0`

## Vault Structure

The vault is organized by algorithm/topic, not by problem source:

- Root `.md` files: major algorithm topics (BFS, DP, 图论, 数学, 构造, 二叉树遍历, 位运算, etc.)
- `数论/` — number theory notes & problems
- `数据结构/` — data structures (segment tree, BIT, etc.)
- `树论/` — tree algorithms
- `动态规划/` — DP problems
- `区间问题/` — interval problems
- `位运算/` — bit manipulation
- `Excalidraw/` — hand-drawn diagrams (Excalidraw plugin)
- `image/` — screenshots and pasted images
- `.trash/` — deleted files

## Note Format

- **Frontmatter**: none (plain Markdown)
- **Internal links**: `[note-name](note-name.md)` or `[note-name](folder/note-name.md)`
- **Math**: `$...$` inline, `$$...$$` block (LaTeX notation)
- **Code blocks**: ` ```c++ ` with full competitive-programming style — do NOT add a separate `C++` text label above the code block; the language is already declared by the fenced ```c++ marker
- **Problem references**: links to OJ platforms (Codeforces, HydroOJ, 牛客, Luogu, etc.)
- **Images**: `![](image/Pasted%20image%20YYYYMMDDHHMMSS.png)` for screenshots, stored in `image/` — use **standard Markdown** syntax (not `![[wikilink]]`) so images render on GitHub. The relative path prefix varies by note depth:
  - Root notes: `image/Pasted%20image%20...`
  - 1-level deep: `../image/Pasted%20image%20...`
  - 2-level deep: `../../image/Pasted%20image%20...`
  - Spaces in filenames must be URL-encoded as `%20`
  - **Git workflow**: When committing note changes, always `git add` the corresponding image files together with the `.md` files — otherwise GitHub will show broken image links. New images (not previously tracked) require explicit `git add`, unlike already-tracked `.md` files which auto-detect changes.
- **Inline code/variables**: wrap variable expressions like `` `pa[x][i]` ``, `` `depth[y]` ``, `` `dp[x ^ i]` `` in backticks — prevents Obsidian's Markdown parser from treating `[x][i]` as a reference link (which would render `pa[x][0]` as `pax`)
- **Markdown beautification** (for readable, structured notes):
  - `# Title` at the top of every note
  - `## Section` / `### Subsection` for hierarchical structure
  - `> **Note:** ...` blockquotes for caveats or key reminders
  - `---` horizontal rules to separate major sections
  - Ordered lists (`1. 2. 3.`) with `.` instead of Chinese commas (`1，2，3`)
  - Wrap operators/comparisons in backticks: `` `a ^ b` ``、`` `a ^ b == 0` ``
  - Use `**bold**` for key terms being defined or emphasized (e.g. **异或空间线性基**)
  - Insert spaces between CJK and alphanumeric characters for readability (e.g. `非 0` not `非0`)
  - **Whitespace hygiene**: use single blank lines to separate content blocks (headings, code blocks, lists, paragraphs) — never leave multiple consecutive blank lines. Strip trailing whitespace from every line. Keep blank lines *inside* code blocks intact (they are part of the code). Preserve a blank line before/after headings and code blocks so Markdown renders correctly.
  - **Code blocks default to expanded (no folding)**: when beautifying/formatting notes, do NOT wrap code blocks in collapsible Callouts or `<details>` unless the user explicitly asks for folding. Keep code blocks fully visible by default. The collapsible Callout pattern below is ONLY used when explicitly requested.
  - **Collapsible full-code templates**: when a note begins with a long complete C++ template (100+ lines), wrap it in a collapsible Obsidian Callout so the Reading View defaults to collapsed:
    ```
    (blank line — required so Obsidian treats the callout as collapsed by default)

    > [!abstract]- 📎 完整代码模板（点击展开）
    >
    > ```c++
    > ... 完整代码 ...
    > ```
    >
    ```
    Rules: every line of the callout (including code fences) is prefixed with `> `. The note MUST start with a blank line before the callout, otherwise Obsidian expands it by default. Do NOT use `<details>` for this purpose — it does not render reliably in Obsidian and breaks code highlighting.
  - **When pasting from external sources**: LaTeX math formulas may have rendering artifacts — plain-text snippets get duplicated alongside the rendered formula (e.g. `E(X)ppE(X)​`). Always clean up: use proper `$$...$$` or `$...$` syntax, fix \frac patterns, and deduplicate repeated expressions.

## Common Tasks

- **Read a note**: `Read file_path="topic.md"` — always check linked notes for related context
- **Search for a problem or technique**: `Grep pattern="keyword" glob="**/*.md"` — most content is in Chinese
- **Add a new note**: use the same naming convention: Chinese descriptive name (e.g. `KMP.md`, `线段树.md`)
- **Edit a note**: use Edit for targeted changes, Write for new files
- **When a note has code + explanation**: the explanation (思路) section precedes the code block — understand the approach before suggesting changes

## Competitive Programming Context

- Problem sources: Codeforces, HydroOJ (HBCPC), 牛客竞赛, Luogu, AtCoder
- Difficulty range: typical OI/ICPC/CCPC provincial to national level
- Focus areas: graph theory, DP, number theory, data structures, constructive algorithms, bit manipulation, trees
