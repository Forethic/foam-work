# OLYD System Instructions

You are operating within the OLYD personal life management system. Your responses should align with the conventions below.

## System Overview

OLYD is an AI-driven system with 12 specialized agents organized into layers:

| Agent          | Layer    | Purpose                                                                         |
| :------------- | :------- | :------------------------------------------------------------------------------ |
| `olyd.day`     | Entry    | Morning dashboard — aggregates tasks, habits, review cards, and daily schedule  |
| `olyd.inbox`   | Entry    | Quick capture — records raw ideas without structure                             |
| `olyd.init`    | Plan     | Strategic spec — generates the Source of Truth (life vision → 3-dimension spec) |
| `olyd.year`    | Plan     | Annual plan — quarterly milestones and key dates                                |
| `olyd.month`   | Plan     | Monthly plan — week decomposition with fixed commitments                        |
| `olyd.week`    | Plan     | Weekly schedule — time blocks with priority stack                               |
| `olyd.task`    | Execute  | Task cards — lifecycle management (todo→doing→done) with ABC priority           |
| `olyd.project` | Execute  | Project records — phases, milestones, health checks                             |
| `olyd.habit`   | Execute  | Habits — streak tracking, point economy (1000 pts = ¥1), reward redemption      |
| `olyd.note`    | Execute  | Knowledge cards — SM-2 spaced repetition by deck                                |
| `olyd.study`   | Execute  | Study plans — phase-based scheduling with review cycles                         |
| `olyd.review`  | Feedback | Multi-cycle retrospectives — day/week/month/year + core principles scoring      |

## Agent Invocation

Agents are invoked via Copilot Chat. Each agent has a mode (e.g., `olyd.task`) and reads/writes to specific folders. Agents do NOT auto-chain — the user manually invokes the next agent in a workflow.

## Folder Structure

| Folder      | Purpose                                                             | Managed By               |
| :---------- | :------------------------------------------------------------------ | :----------------------- |
| `meta/`     | System constitution (core principles, ABC method, file conventions) | Manual                   |
| `inbox/`    | Capture buffer                                                      | `olyd.inbox`             |
| `specs/`    | Strategic specification                                             | `olyd.init`              |
| `journal/`  | Plans + journals + reviews (YYYY/MM/Week-XX)                        | `year/month/week/review` |
| `tasks/`    | Task card kanban (0_todo/1_doing/2_done)                            | `olyd.task`              |
| `projects/` | Project records                                                     | `olyd.project`           |
| `areas/`    | Life responsibility domains                                         | Manual                   |
| `habits/`   | Habit cards + ledger (\_ledger.md, \_rewards.md)                    | `olyd.habit`             |
| `notes/`    | Knowledge cards (cards/) + deck indexes (decks/)                    | `olyd.note`              |
| `study/`    | Study plans + session logs                                          | `olyd.study`             |
| `archive/`  | Completed/archived items                                            | Manual                   |

## Priority Stack

1. 🏥 Health — non-negotiable baseline, weight-loss sprint (Jul–Aug)
2. 📋 Career / Certification — highest cognitive investment
3. 🏠 Family — child education, chores, spouse time
4. ☕ Restoration — games/mahjong/sports within quota; zero during sprint phases
5. 🔍 Exploration / Mission — residual time only

## Icon Semantics

| Icon | Category           | Meaning                                            |
| :--- | :----------------- | :------------------------------------------------- |
| 🟢   | Growth / Execution | Active output: work, study, cert prep              |
| 🔍   | Exploration        | Research, ambiguous projects, early-stage          |
| 🏠   | Maintenance        | Chores, errands, recurring responsibilities        |
| ☕   | Restoration        | Games, sports, travel, social recharge             |
| 🔴   | Depletion          | Fragmented time, idle waiting — must convert to 🟢 |

## Core Principles (Daily Scoring 0-10)

认真 · 快 · 坚守承诺 · 保证完成 · 乐观 · 自信 · 爱与奉献 · 绝不找借口

## Key Rules

- Plan agents (init/year/month/week) do NOT handoff to review — review is user-initiated
- Task status is tracked via individual card files, not a centralized index
- Habit points are permanent; streak bonuses are multipliers (7d ×2, 30d ×3, 100d ×5)
- SM-2 spaced repetition: cards scored 0-5, interval adjusted by ease factor
- All agents use the Termination Rule: stop after summary, never auto-chain
