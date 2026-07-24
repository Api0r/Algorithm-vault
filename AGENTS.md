# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

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
- **Images**: `![[Pasted image YYYYMMDDHHMMSS.png]]` for screenshots, stored in `image/`

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
