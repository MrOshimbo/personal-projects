# Personal Projects — Context & Continuity

**Repository:** https://github.com/MrOshimbo/personal-projects

## Purpose

This repository is the source of truth for the user's personal projects: their purpose, evolving plans, decisions, status, and next steps. It is intended to make it easy to resume work after a gap without reconstructing the overall picture from conversation history.

The portfolio is about building, exploring, and improving things that matter personally—not just collecting task lists.

## Mindset and tone

Approach every personal project with a positive, encouraging, and energetic mindset. The goal is to make the work feel exciting, motivating, and enjoyable—not like another obligation or source of pressure.

When discussing ideas, progress, challenges, or next steps:

- Lead with curiosity, optimism, and a can-do attitude.
- Recognize progress and celebrate meaningful wins, including small steps forward.
- Encourage exploration and creative thinking; help turn obstacles into manageable problems and opportunities to learn.
- Keep the energy constructive and forward-looking, with practical next steps that build momentum.
- Be supportive without being unrealistic: communicate risks, trade-offs, uncertainty, and setbacks honestly, but frame them in a calm, solution-oriented way.
- Collaborate as an enthusiastic partner. Help the user feel capable, energized, and excited to keep moving.

This positive tone should be the default across all projects and conversations about the personal-projects portfolio.

## Portfolio at a glance

The repository currently contains three distinct projects:

1. **AI Dunny** — a hands-on hardware and software project to build a physical conversational AI character.
2. **Umbrella LLC** — an exploratory business concept for a possible umbrella LLC supporting multiple ventures, with paid events for bands as the first concrete idea.
3. **Upgrade 2.0** — a whole-life personal development project organized around four pillars: health and well-being, financial well-being, career and personal growth, and relationships and experiences.

Each project has its own README. Those project READMEs are the detailed, current sources for their respective plans and status.

## Project summaries and current direction

### AI Dunny

**Goal:** Build a physical conversational AI character inside a customized Dunny-style figure.

**Approach:** Develop incrementally, beginning with a working voice-capable bench prototype before adding portability, the physical body, motors, movement, and more advanced character behaviors.

**Current direction:** The ESP32-S3 Realtime audio path has been demonstrated with separate microphone channels, server-side voice activity detection, and speaker playback. The immediate engineering step recorded in the project README is to test a combined mono signal from both microphones and compare it with the individual-channel results. The planned Adafruit Metro/ICS-43434 target setup remains to be validated; current bench results use a Waveshare ESP32-S3 board and SPH0645 microphones.

**Detailed source:** [projects/ai-dunny/README.md](projects/ai-dunny/README.md)

### Umbrella LLC

**Goal:** Explore whether an umbrella LLC could provide a shared business structure for multiple ventures over time.

**First concrete concept:** Organizing and producing paid events for bands.

**Status:** Exploratory idea only. No decision to form an LLC has been made. Open questions include the right legal structure, possible DBAs or separate entities, liability and insurance, licensing, taxes, accounting, contracts, and the economics of live events. Seek qualified legal and tax advice before making formation decisions.

**Detailed source:** [projects/umbrella-llc/README.md](projects/umbrella-llc/README.md)

### Upgrade 2.0

**Goal:** Make meaningful, sustainable improvements across life as a whole—to feel better, live better, and become the person the user wants to be, without turning life into another work assignment.

**Guiding principle:** *Improve the person, improve the life—not just the résumé.*

**Four pillars:**

1. **Health & Well-being** — therapy and mental health, exercise and fitness, diet and nutrition.
2. **Financial Well-being** — credit, accounting and organization, financial planning.
3. **Career & Personal Growth** — professional development, education and continuous learning, skills and intellectual exploration.
4. **Relationships & Experiences** — relationships and quality time, friendships and social connections, experiences and enjoyment, and meaningful moments.

**Working approach:** Favor sustainable changes, focus on a few concrete goals at a time, track progress, and let the pillars evolve. The next step is to translate the pillars into specific goals and practical actions.

**Detailed source:** [projects/upgrade-2-0/README.md](projects/upgrade-2-0/README.md)

## Collaboration preferences

When helping with this portfolio:

- When the user refers to "my personal projects," "the personal-projects repo," or asks to resume the portfolio, start by consulting this file and the root README. Then consult the relevant individual project README before making project-specific claims or changes.
- Treat the GitHub repository and project READMEs as the canonical source of truth for project details and status; use conversation context to understand the user's latest intent and decisions.
- Preserve important decisions, rationale, milestones, and next steps in the relevant project README so they remain available across conversations.
- Keep the root README concise as the portfolio index. Keep this file focused on cross-project context and continuity. Keep technical or detailed project material in each project's own README.
- Do not make repository changes, create external commitments, or take consequential actions without the user's authorization.
- Distinguish confirmed results from plans, assumptions, and open questions. Do not present exploratory ideas as decisions.
- When resuming a project, briefly orient the user to its current status and the next meaningful step rather than restarting from scratch.

## Portfolio history

- **2026-11-11:** Established personal-projects as the home for personal project documentation, separate from the user's work-ideas repository.
- **2026-11-11:** Added **Umbrella LLC** to preserve the idea of a possible umbrella business structure, initially exploring paid events for bands.
- **2026-11-11:** Added **Upgrade 2.0** with three initial pillars: Health & Well-being, Financial Well-being, and Career & Personal Growth.
- **2026-11-11:** Expanded Upgrade 2.0 to four pillars by adding Relationships & Experiences.
- **2026-11-11:** Refined the first focus area under the fourth pillar to **Relationships and quality time**, rather than limiting it to family.

## Keeping this file current

Update this context file when the portfolio's overall purpose, project lineup, cross-project priorities, or collaboration approach changes. Record project-specific decisions and detailed progress in the relevant project README, then update this overview when those changes materially affect the portfolio snapshot.
