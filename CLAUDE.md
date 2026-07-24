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
- **Code blocks**: ` ```c++ ` with full competitive-programming style
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
