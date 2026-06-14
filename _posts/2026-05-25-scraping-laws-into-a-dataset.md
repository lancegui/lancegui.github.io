---
layout: post
title: "Scraping laws into a research dataset with AI agents"
date: 2026-05-25 12:00:00
description: "How I use AI coding agents, a browser tool, and a written plan to build auditable 50-state legal datasets that do not exist yet."
card_blurb: "AI makes web scraping easy."
card_link_text: "read the guide →"
tags: ai research scraping legal-data economics
categories: research
toc:
  beginning: true
---

Economists often need datasets that do not exist yet. Suppose you want to study a law across all fifty states. The hard part is usually not the regression. The hard part is building the dataset from scratch, one statute at a time.

This post is about doing that with AI coding agents: a browser tool to reach the pages, parallel agents to cover the states, and a written plan so the work stays auditable. It pairs with [my local model guide](/blog/2026/ai-research-workflow/) and [my post on building research skills](/blog/2026/merging-ai-skills-into-your-field/), but it stands on its own.

## What a legal dataset has to answer

When you study a law across states, the dataset has to answer a long list of questions, and each one is a column you have to fill by hand or by agent:

- Which states have the law?
- When did the law pass?
- When did it take effect?
- What exact statutory text changed?
- Which definitions matter?
- Are there thresholds, exemptions, or sunset dates?
- Did later amendments change the treatment?

This is exactly where AI helps, but only if you use it carefully. The model is good at reading a statute and pulling out fields. It is dangerous when it guesses instead of flagging that it does not know.

## A workflow that works

My preferred workflow looks like this:

1. Use a planning skill, `/agent-skills`, or `/superpowers` to create a plan.
2. Design the schema before scraping: variables, enactment dates, effective dates, legal definitions, petitions, exemptions, and sources.
3. Tell the agent to use Git before editing code or data files.
4. Save the plan locally. If the agent does not save it, explicitly ask it to create a plan file.
5. Run the workflow on a few states first.
6. Review the output and revise the plan. Some laws will not look like what you expected.
7. Use parallel agents only after the pilot looks reasonable.
8. Use Playwright MCP when ordinary scraping gets stuck on browser interactions.
9. Ask the agent to write a summary of what it found, what failed, and what still needs human review.
10. Repeat until the dataset is good enough to audit.

There will be problems. In web scraping, there are always problems. Browsers fail, pages change, legal search portals behave strangely, and some states organize statutes in surprising ways. That is why the plan and summary must be saved locally. The model will drift less, and you will know what happened when you return to the project later.

Your job is to keep the plan updated, keep the summary updated, and create enough checkpoints that one bad pass does not ruin the project.

## Use a browser tool for hard sites

Many research tasks require the agent to do something outside the chat box. Many websites are not simple static HTML pages either. Some require clicks, search boxes, login flows, or JavaScript, and a plain scraper stalls on them.

The tool I reach for is [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp). Playwright is a browser automation tool. With Playwright MCP, an agent can open a real browser, click buttons, fill forms, and inspect pages. It should still respect logins, terms of service, and access rules.

Copy-paste install for Claude Code:

```bash
claude mcp add playwright npx @playwright/mcp@latest
```

This command assumes Node.js is installed, because it uses `npx`. If your terminal says `npx` is missing, install Node.js first and try again. You can also just ask Claude Code to install `microsoft/playwright-mcp` for you.

After restarting Claude Code, test it:

```text
Use playwright to navigate to https://example.com and tell me the page title.
```

If that works, the agent has a browser tool. This is a large change for scraping. If you have used Selenium before, you know how annoying it is to figure out which button maps to which script. Now the model can often operate the browser directly, and it can use text understanding while it navigates. That matters when the same legal idea is described in different words across states.

## Parallel agents and Git checkpoints

Once the schema is locked, the work is embarrassingly parallel: one batch of states per agent. Instead of one model slowly doing everything, you can send smaller jobs to several agents at once: one checks sources, one writes a scraper, one reviews output, one drafts documentation. Skills make this cleaner, because each agent loads the same instructions. I keep my own research habits as [skills](/blog/2026/merging-ai-skills-into-your-field/) so the agents stay consistent.

Git matters here even for non-technical readers. Git is version control. It lets you save checkpoints and go back when something breaks. The important idea is simple: before letting an AI edit a project, make it easy to undo the edits. That is better than trusting a rewind button in a chat app.

For a new project folder, the basic pattern is:

```bash
git init
git add .
git commit -m "Start project"
```

After that, ask the agent to check `git status` before it edits files and to commit important checkpoints as it goes.

## Watch the cost

Once you add a browser tool and parallel agents, AI can get expensive fast.

I once burned through a five-hour Claude Pro session in about ten minutes by scraping websites and running agents in parallel. That is funny once. It is less funny if it becomes your research workflow.

There are three ways to handle this:

1. Pay for a larger subscription.
2. Use pay-as-you-go APIs.
3. Route cheaper tasks to cheaper or local models.

The third option is the most interesting. Use the expensive frontier model for the parts that need judgment. Use local or cheaper models for the repetitive parts: page extraction, cleaning, first-pass classification, logging, formatting, and sanity checks.

The rule of thumb is simple:

> Spend frontier-model attention where judgment matters. Spend cheap-model attention where repetition matters.


## Copy-paste plan template

Here is the kind of `plan.md` I want the agent to create before serious scraping starts. This is adapted from a real project plan, but I removed the specific law and issue area. The point is the structure.

<details>
<summary><strong>Open generic <code>plan.md</code> template</strong></summary>

<div markdown="1" style="max-height: 520px; overflow: auto; border: 1px solid var(--global-divider-color); padding: 1rem; margin-top: 1rem; border-radius: 6px;">

````markdown
# Implementation Plan: 50-State Legal Dataset

**Status:** Draft
**Goal:** Build a spreadsheet-ready legal dataset across all 50 states plus DC.
**Sources:** Official legislature, judiciary, and agency websites only.
**Research use:** Difference-in-differences, event study, descriptive legal mapping, or policy surveillance.

---

## Overview

Build a legal dataset covering whether, when, and how each jurisdiction regulates the target policy. The final output should support empirical research and be auditable by another researcher.

The dataset should include:

- Statute citations
- Official source URLs
- Enactment or effective dates when available
- Core legal requirements
- Exceptions or exclusions
- Procedural steps
- Notes for ambiguous cases
- A provenance trail for every coding decision

---

## Architecture Decisions

| Decision | Rationale |
|---|---|
| Start with well-documented states | Calibrates the schema before scaling |
| Use official sources for final coding | Improves reproducibility and legal accuracy |
| Save raw sources before cleaning | Makes the dataset auditable |
| Pilot before parallelization | Prevents scaling a bad schema |
| Use explicit coding conventions | Makes regression variables easier to construct |
| Use Git checkpoints | Makes AI edits reversible |

---

## Output Schema

| # | Column | Type | Coding Convention |
|---|---|---|---|
| 1 | State | String | Full state name |
| 2 | Policy_Status | Categorical | Yes / No / Unclear |
| 3 | Statute_Citation | String | Official citation or "No statute found" |
| 4 | Statute_Title | String | Official title or section heading |
| 5 | Year_Enacted | Numeric/String | Year or "Not stated" |
| 6 | Effective_Date | String | Date or "Not stated" |
| 7 | Key_Definition | String | Relevant statutory definition |
| 8 | Covered_Population | String | Who is covered |
| 9 | Requirement_1 | Categorical | Yes / No / Not stated / Unclear |
| 10 | Requirement_2 | Categorical | Yes / No / Not stated / Unclear |
| 11 | Exception_1 | Categorical | Yes / No / Not stated / Unclear |
| 12 | Enforcement_Agency | String | Agency or "Not stated" |
| 13 | Procedure_or_Process | String | Short description |
| 14 | Penalty_or_Consequence | String | Penalty, consequence, or "Not stated" |
| 15 | Official_Form_Found | Categorical | Yes / No / Local only |
| 16 | Form_Name | String | Official title or "Not found" |
| 17 | Statute_URL | String | Official URL |
| 18 | Form_URL | String | Official URL or "Not found" |
| 19 | Notes | String | Ambiguity, caveats, legal interpretation |
| 20 | Search_Terms_Used | String | Useful for failed searches |
| 21 | Date_Accessed | Date | Date source was accessed |

---

## Official Source Strategy

For each state, search in this order:

1. Official legislature or code website
2. Official judiciary website
3. Official agency website
4. State search portal
5. County or local pages only if statewide forms are not available

Do not use secondary sources for final coding unless explicitly marked as a cross-check.

---

## Task List

### Phase 0: Schema + Setup

- [ ] Finalize column schema
- [ ] Create CSV template with headers
- [ ] Create raw source folder
- [ ] Create notes folder
- [ ] Create `README.md` for the dataset
- [ ] Commit the empty structure with Git

### Checkpoint 0

- [ ] Schema reviewed
- [ ] Coding conventions defined
- [ ] Source rules written down

---

### Phase 1: Pilot States

Choose 3 to 5 states that are likely to be well documented.

For each pilot state:

1. Locate official statute
2. Save raw HTML or PDF
3. Code every schema field
4. Search for official forms or agency guidance
5. Record URLs and date accessed
6. Flag unclear fields

### Checkpoint 1: Schema Calibration

- [ ] Review pilot codings
- [ ] Identify confusing fields
- [ ] Revise schema if needed
- [ ] Update coding rules before scaling

---

### Phase 2: Batch Research

Split the remaining states into batches.

Example:

- **Batch A:** 10 states
- **Batch B:** 10 states
- **Batch C:** 10 states
- **Batch D:** 10 states
- **Batch E:** Remaining states plus DC

Each batch agent should:

1. Search official sources
2. Save raw files
3. Code structured rows
4. Record uncertainty in `Notes`
5. Avoid guessing
6. Return a CSV fragment and a short summary

### Checkpoint 2: Batch QC

- [ ] Every state has a row
- [ ] Every row has an official source URL or explicit "No statute found"
- [ ] Ambiguous fields are marked "Unclear"
- [ ] Notes explain uncertainty
- [ ] URLs are official sources

---

### Phase 3: Consolidation + Quality Control

- [ ] Merge batch CSV files
- [ ] Check duplicate states
- [ ] Check missing citations
- [ ] Check inconsistent coding values
- [ ] Cross-check against secondary sources only as validation
- [ ] Create summary lists:
  - States with no clear statute
  - States with ambiguous coding
  - States needing manual review
  - States with missing official forms
  - States with unusual legal structures

### Checkpoint 3

- [ ] CSV loads cleanly in R or Python
- [ ] No unexpected missing values
- [ ] All URLs spot-checked
- [ ] Manual-review list created

---

### Phase 4: Final Deliverables

- [ ] Write final CSV
- [ ] Write methodology note
- [ ] Write data dictionary
- [ ] Write limitations section
- [ ] Update project `README.md`
- [ ] Commit final outputs with Git

---

## Risks and Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Official URLs break | High | Save raw files and date accessed |
| Legal language varies across states | High | Use `Notes` and mark unclear fields |
| Some states use unusual terminology | Medium | Save search terms and alternate terms |
| Forms are local rather than statewide | Medium | Mark "Local only" and record locality |
| AI infers too much | High | Tell the agent to flag uncertainty instead of guessing |
| Parallel agents code inconsistently | High | Pilot first, then lock schema |

---

## Parallelization Map

```text
Phase 0: setup
  |
  +-- Phase 1: pilot states
        |
        +-- Checkpoint 1: schema calibration
              |
              +-- Phase 2: parallel state batches
                    |
                    +-- Phase 3: merge + QC
                          |
                          +-- Phase 4: final deliverables
```

---

## Acceptance Criteria

- [ ] One row per jurisdiction
- [ ] Official citation or explicit "No statute found"
- [ ] Official URL for each positive coding
- [ ] Ambiguous cases marked "Unclear"
- [ ] Raw sources saved
- [ ] Dataset loads cleanly in R or Python
- [ ] Methodology note explains source rules
- [ ] Manual-review list exists
- [ ] Git history contains checkpoints
````

</div>
</details>

**LLMs are sprinters, not marathon runners.** The trick is to build a track where many short sprints add up to real research.
