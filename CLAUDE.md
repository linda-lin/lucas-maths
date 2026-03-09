# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file math practice app for a child (LH). No build step, no dependencies, no package manager — just `lh-math.html` opened directly in a browser.

## Architecture

Everything lives in `lh-math.html`:

- **CSS** (`<style>`) — uses CSS custom properties (`--coral`, `--mint`, `--blue`, etc.) for the colour palette. Responsive via CSS Grid and `clamp()`.
- **HTML** — three view areas toggled with `display:none/block`:
  - `#setup-area` — operation selector, level pills, question count, history table
  - `#quiz-area` — timer bar, progress bar, question grid (`#q-grid`), submit button
  - `#result-area` — score card, wrong-answer review, correction round (`#correction-section`)
- **JavaScript** (inline `<script>`) — no framework, plain DOM manipulation.

### Key state variables
| Variable | Purpose |
|---|---|
| `activeOps` | `Set` of selected operations (`'add'`, `'sub'`, `'mul'`, `'div'`) |
| `currentLevel` | `1.1`, `1.2`, `2`, or `3` |
| `questions` | Array of question objects generated at quiz start |
| `pendingCorrections` | Array of question indices still needing correction after submit |
| `quizConfig` | Snapshot of settings used for the current quiz |

### Level system
`levelIndex(lv)` maps `1.1→0, 1.2→1, 2→2, 3→3`. All per-operation difficulty arrays use this index. Division levels: L1.1 = 2-digit ÷ 1-digit, L1.2 = 2-digit ÷ 2-digit, L2 = 3-digit ÷ 1-digit, L3 = 3/4-digit ÷ 2-digit.

### Quiz flow
`startQuiz()` → generates `questions[]`, renders `#q-grid` cards → `submitQuiz()` grades answers, populates `#review-section`, calls `buildCorrectionSection()` if any mistakes → `checkCorrections()` iterates `pendingCorrections` until empty → `resetQuiz()` resets all state and returns to setup.

### Persistence
History stored in `localStorage` under key `lh_math_v3` as a JSON array (max 50 entries).
