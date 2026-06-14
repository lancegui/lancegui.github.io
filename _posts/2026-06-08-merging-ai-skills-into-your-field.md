---
layout: post
title: "Integrate multiple battle-tested software agent skills into a workflow in economics"
date: 2026-06-08 12:00:00
description: "The software agent skills were useful but unsatisfying on real data, so I wrote a new set for data's silent failures and combined four battle-tested skill systems (superpowers, Karpathy's notes, ECC, and planning-with-files) into Causal Powers, a Claude Code plugin for economics."
card_blurb: "I merged battle-tested AI agent skills into one economics workflow."
card_link_text: "click here →"
tags: ai skills economics causal-inference llm
categories: research
toc:
  beginning: true
---

People are building "skills" for AI coding agents: small instruction packets that tell the agent how to do a repeated task. If MCP gives a model hands, skills give it habits. Almost all of them are built for software engineering. Testing, debugging, code review, git.

I work in economics, not software. When I first ran these software skills on real research, the discipline was genuinely useful, but it was unsatisfying the moment it touched real data. The workflow was built to catch bugs that crash. The bugs that end careers in data work do not crash; they run clean and hand you a confident, wrong answer. So I did two things: I wrote a new set of skills built for those silent failures, and I combined the few outside skills that turned out to be instrumental. The result is [Causal Powers](https://github.com/lancegui/causal-powers), now a Claude Code plugin for data analysis, causal inference, and econometrics.

> **The whole idea:** you do not have to build a skill, or even pick one. Borrow several, mix in what you already know about your field, and iterate on real work until the result is yours.
{: .block-tip}

This post is how that went, including what I got wrong.

## Three quick terms

**Skill:** an instruction packet the agent loads when it recognizes a task. A checklist that activates itself.

**Hook:** something the harness runs automatically on an event, like the start of a session. A skill is on-demand; a hook is always-on.

**Subagent:** a fresh helper you hand one isolated job, so the main agent can stay on the plan.

The rest is built from those three.

## The skills I borrowed from

**[superpowers](https://github.com/obra/superpowers)**, by Jesse Vincent, gave me the skeleton. Its idea is that skills are not suggestions but mandatory workflows that fire before the agent acts: brainstorm before you build, write the test before the code, debug systematically, pass a review gate before calling something done. It ships the whole apparatus: a gateway skill that routes to the others, a session hook, subagents, and review gates. I kept the shape and threw out the software.

**[Andrej Karpathy's notes](https://github.com/multica-ai/andrej-karpathy-skills)** (packaged by multica-ai; Karpathy did not write them, someone turned his observations into a skill) gave me the craft. The observations are blunt: models make wrong assumptions and run with them, overcomplicate, leave dead code, and edit things they do not understand. The answer is four habits: think before coding, keep it simple, change only what you must, and loop until a clear success criterion is met. In data work that became one rule I lean on: write the minimum analysis that answers the question, and edit a colleague's notebook surgically instead of "improving" parts you were not asked to touch.

**[ECC](https://github.com/affaan-m/ecc)**, by Affaan Mustafa, gave me the always-on layer. Skills only fire when the agent recognizes the task, but some discipline has to hold every time. ECC's layered design (and superpowers' own session hook) showed me how: a small block of non-negotiable rules injected at the start of every session, so the discipline is the default instead of something the agent has to remember.

**[planning-with-files](https://github.com/othmanadi/planning-with-files)**, by Othman Adi, gave me memory. Long analyses outrun the context window, and when the session compacts, decisions quietly get lost. Its idea is to keep the plan on disk: a written `analysis-plan.md` the agent ticks off and reloads, so the work survives a `/clear` or an auto-compaction. In data work that became the living plan every multi-step analysis writes before it runs, with a decisions log that records why each cleaning choice was made.

None of this was a clean inheritance. I took the skeleton from one, the craft from another, the always-on layer from a third, and persistent memory from a fourth. The judgment about my own field was the part I had to add.

## The one move that made it work

All four were built for software. None of them knew anything about a regression. What let them transfer was one observation:

**In software, the dangerous bug is loud. In data analysis, it is silent.**

Wrong code usually throws: a stack trace, a red test, a crash. The software-skills tradition is built to make that failure happen early and catch it. The analysis bug that ends careers does not throw. It just sits there:

- A join fans out and revenue triples.
- One missing value poisons a mean.
- Units are off by 100 and the chart still looks fine.
- The test set leaks into training and the model scores beautifully.
- Confounding slips in wearing the costume of a causal effect.

None of these raise an error. The code runs clean and hands you a confident, wrong answer.

> A number you computed but never validated is a guess wearing a lab coat.

So I kept the form and replaced the failure mode. The skeleton, the craft, the always-on layer, and the plan on disk do not care what field you are in, so they stayed. The software content went, and I re-authored each skill around the silent failures of data work and the judgment a senior applied economist uses without thinking. The map was almost one-to-one:

<div class="table-responsive">
<table class="table table-bordered table-hover">
  <thead>
    <tr>
      <th>The software skill</th>
      <th>Became, for data work</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Brainstorm before building</td>
      <td>Pin the <strong>estimand</strong> (the quantity you are trying to estimate), the population, and the decision before any code</td>
    </tr>
    <tr>
      <td>Test-driven development</td>
      <td><strong>Data contracts</strong>: check join cardinality and reconcile totals before trusting a number</td>
    </tr>
    <tr>
      <td>Systematic debugging</td>
      <td>Bisect the pipeline to the step where the number went wrong</td>
    </tr>
    <tr>
      <td>Verify before calling it done</td>
      <td>Reconcile to source and reproduce from a clean session before reporting</td>
    </tr>
    <tr>
      <td>Code review</td>
      <td>Review for the silent killers: fanned-out joins, leakage, unreconciled totals</td>
    </tr>
    <tr>
      <td><em>(no software analog)</em></td>
      <td>State and test the <strong>identification</strong> assumptions before estimating an effect</td>
    </tr>
  </tbody>
</table>
</div>

The last row is the whole reason it was worth doing. Every field has something with no analog in the source material. Mine was causal identification: the argument for why a correlation deserves to be read as cause and effect. The borrowed scaffolding gave me somewhere to put it.

## What the build actually looked like

It was not tidy, and it was not slow. About a week, in messages I typed between real analyses.

It started with one:

> do you see superpower skills? I want to create a variant of superpower skills adapted for data analytics, causal inference, and econometrics specifically. Currently superpower is for software development first, but there are problems specific to data analytics, particularly in merging data (silently dropping things) ... the test then needs to check common fragile data problems like merge joins: what do we expect, many-to-many or one-to-many? Languages specific to R, Julia, and Python.

That is the whole thesis, before I tidied it up for a blog post. The first version was not even a family, just one catch-all skill called `validation-driven-analysis` that was too big to edit without fear and too vague to trigger reliably. I used Claude Code's built-in [`skill-creator`](https://github.com/anthropics/skills/tree/main/skills/skill-creator), the skill that makes skills, deleted the old one, and rebuilt it as a family with superpowers' structure as the mold.

From there the loop never changed: use the latest version on real research, watch what it gets wrong, and feed the fix back through skill-creator. Real work is the only honest test. Almost every skill in the family exists because something broke during an actual analysis.

skill-creator makes that loop fast. It drafts a skill, runs it against test prompts, and rewrites whatever misfires. On one skill it found that my trigger description "reads like a methodology lecture" and never fired on the tasks it was for, so it rewrote the description to name what the user actually wants. You only catch that by testing.

The merges came one at a time. The instinct that mattered most was already in the prompt I wrote when I got to ECC:

> going through this exhaustively, learning each bit. do you think I can further improve my current skills? ecc right now seems to have too many elements that I don't need, but what could you learn from these principles?

"Too many elements that I don't need" is the whole trick. ECC ships hundreds of skills and dozens of agents. I took two ideas and left the rest.

The mistakes showed up the same way, in real use. After one version:

> it does not write a spec anymore, and after writing the plan it does not stop to ask me what I think about the plan. it is making decisions behind my back, which is a violation of the karpathy principles.

It had started quietly redesigning my analysis instead of asking. That complaint became a skill of its own: a checkpoint that forbids changing the design, the sample, or the specification behind my back. Then, once I gave it parallel subagents and robustness checks got nearly free:

> it runs a godly amount of robustness checks without supervision. the idea is it will propose at most 3 of the most important robustness checks, not run a menu of free foods.

A wall of robustness checks is a sign an economist does not trust the result, not proof you should. The newest version proposes the two or three that would actually break the result, then stops. The capability I was proudest of was the one I had to rein in.

Somewhere in there I also just told it to "mimic a senior econ professor at MIT." That was the part none of the borrowed sources could give me, and it did the real work.

What started as one catch-all skill is now fourteen, wired into a plugin. There is an always-on layer injected at the start of every session, a router that re-surfaces the right skill on each prompt, a stop-gate that will not let me call an analysis done before it is verified, and subagents that fan robustness checks out in parallel. It also grew past reduced-form work. There is now a structural-estimation skill that makes me prove an estimator recovers known parameters by Monte Carlo before I trust it on real data, and re-solve equilibrium for a counterfactual instead of holding prices fixed. That, like causal identification, has no analog in the software skills I started from. It was the second thing only I could add.

## If you want to try it in your field

The economics is incidental. The shape is not:

1. **Borrow, do not build.** Find a few skill systems you like. They will be built for software. Take the structure, not the content.
2. **Add what only you know.** Work out your field's silent failures: what goes wrong without announcing itself, what an expert checks on instinct. That is the actual work, and no one can do it for you.
3. **Iterate on real work.** Use the skill on a real task, watch what breaks, and feed the fix back through skill-creator. Each real failure is the next skill.
4. **Keep an always-on layer, and a plan on disk.** A few rules must never slip, and a long analysis should not lose its plan when the context compacts.
5. **Credit your sources.** Skills are a commons.

Two examples from fields that are not mine. In law, a precedent that has been quietly overruled looks just like one that still stands, so the analog of a data contract is a check that a citation is still good law. In a lab, a batch effect (a result that tracks the day or the machine, not the biology) looks just like a discovery. Different field, same shape: find the failure that stays quiet, and build the skill that makes it loud.

I did not build much here. Other people built a skill format, a sense of craft, an always-on layer, and a way to keep a plan on disk, and put them online for their own reasons. I added what I know about where data analysis quietly breaks, and a week of using the result until it fit. [Causal Powers](https://github.com/lancegui/causal-powers) is on GitHub, built on [superpowers](https://github.com/obra/superpowers), [Karpathy's notes](https://github.com/multica-ai/andrej-karpathy-skills), [ECC](https://github.com/affaan-m/ecc), and [planning-with-files](https://github.com/othmanadi/planning-with-files). You can install it with `/plugin marketplace add lancegui/causal-powers`. Borrow from it the way I borrowed from them.
