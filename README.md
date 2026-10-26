# About Skillhub

Skillhub shares skills that have been used and tested in real work, together with our introduction to each of them and our assessment.

## Purpose

The right to think and to review is not handed to the AI.

We do not think an AI solves a large problem in one move. It is reliable at precise, repeated labour within a single step: setting each week's new figures into a report in the same layout, checking every number on a page against its source, or drawing a figure to a fixed standard. A skill here therefore takes over one step of a piece of work. The other steps stay visible to a person, and each step can be reviewed by a person at little cost.

It follows that we do not take in skills that generate a whole product from one sentence. Such a skill may be useful for trying out an idea early in a project, and some of them do rest on templates, tools, or rules of real value. When that is so, we study those assets, and if they can serve one of our steps, we rework them into a skill the size of that step. The one-sentence skill itself is not taken in.

If you do want to explore an idea with a one-sentence skill, there is no need to look for it in Skillhub; ask an AI to look on GitHub instead. Exploration of this kind is used once and then set aside, so it does not need our testing or our rework. A prompt such as the following will do:

```
I want to do xxx, and for now I only want to find out whether the idea is workable. Could you find me three to five skills or repositories on GitHub that generate this kind of product directly? For each candidate, tell me what it does, what input it needs from me, when it was last updated, and what its license is. Do not install anything yet; wait until I have chosen.
```

We also take in skills of engineering analysis. When we receive a large demo, we need to take its contents apart according to the kind of demo it is, write a review from which a person can understand it, and refactor it only after a person has read that review. Each of these steps may need a skill of its own, and different kinds of demo need different skills and workflows. Even if a Minecraft-like game really were generated from one sentence, for example, we would still need to know how to change its rules and how to replace its textures later. Whether a person can make such a change by following the review is the test of whether a skill of this kind is good enough.

Exploratory skills may be taken in, such as skills that touch on design, whose output is often only a direction. What separates them from one-sentence generation is that they keep a person in the loop: the AI produces a draft, and a person still judges it and settles the final form.

## Contents

- Blog posts: judgements, written for people.
- Skills: manuals, written for the AI.

### Blog posts

Each blog post (one `.md` document) introduces one or more skills. It covers how to use them, how much work a person still has to put in after a run, why a highly capable model is needed rather than a weaker one, and when the skills should not be used. The assessment in a post is made at a certain time and for certain models, and it may change when the models change. Every post therefore gives its date and the models used in testing.

The front matter of a post, described by analogy in TypeScript:

```typescript
type BlogMeta = {
    title: string;
    slug: string;
    description: string;
    date: string; // YYYY-MM-DD
    skills: string[]; // the name of each skill the post introduces
    models: string[]; // the models used in testing
}
```

A post does not carry its own `useCases`. They are derived from the skills it introduces, so that the two cannot disagree.

### Skills

The structure, metadata, and rework of a skill are described below.

## How skills are chosen

A skill must have been used and tested in real work, and it must be useful for one of our use cases. Beyond that, its `README.md` must answer four questions.

1. Which step of which piece of work does it take over?
2. What does it receive, and what does it hand back?
3. How is the result known to be right? A check that can be run is preferred to a check described in words.
4. How often does the step recur?

A skill that cannot answer these four questions is not taken in.

## The structure of a skill

We never take in a skill simply by putting it here. Every skill has the following structure.

```
<skill-name>/
    - config/
    - SKILL.md
    - README.md
```

The parts divide the work as follows.

- `SKILL.md` is the manual for the AI. Its front matter uses only the fields of the Agent Skills specification: `name`, `description`, and optional fields such as `license`. `name` is the unique identifier of the skill. It must match the directory name and use only lowercase letters and hyphens. `description` states when the skill should be used and when it should not.
- `README.md` is the account for people, and it states only facts: the origin, the changes, how to install, the input and output, and the check. The metadata the site needs is written in its front matter rather than in `SKILL.md`, so that the `SKILL.md` installed in a user's environment stays clean.
- `config/` holds the material the skill reads when it runs, such as templates, donor material, and check scripts.

The front matter of `README.md`, described by analogy in TypeScript:

```typescript
type SkillMeta = {
    title: string; // not the unique identifier, but still not to be repeated, so that users are not confused by names once there is a front end
    name: string; // the same as the name in SKILL.md and the directory name
    kind: "repetitive" | "exploratory"; // repeated labour, or exploration
    humanEffort: Score | [Score, Score]; // see below; a range gives the spread across inputs or models
    modelIQ: "low" | "medium" | "high";
    useCases: UseCase[];
    origin?: { // only for a skill taken from elsewhere and reworked
        repo: string;
        commit: string;
        license: string;
    };
}

type Score = 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10;

type UseCase =
    | "weekly-report"
    | "report"
    | "dashboard"
    | "long-form-reading"
    | "talk-deck"
    | "demo-review";
```

### humanEffort

`humanEffort` measures only how much work a person still has to put in after one run. Whether a skill is exploratory is stated separately, by `kind`.

The score runs from 0 to 10 and is read against four anchors.

| Score | Anchor |
| --- | --- |
| 0 | The output can be used as it is |
| 3 | The output only needs review |
| 7 | The output needs a person to change it by hand |
| 10 | The output is only a draft or a direction, and a person still does the main work |

The anchors are not evenly spaced. The move from review to changing the output by hand is a change in kind, and a person's work rises sharply there, so the widest gap lies between 3 and 7. A score between two anchors means that the skill lies between them, and it shows which of the two it is nearer.

There are two kinds of uncertainty, and they are written differently. If a skill lies steadily between two anchors, a single value is written, for example `humanEffort: 5`. If its results vary with the input or the model, so that it needs only review when the input is clear and the model is strong enough, and otherwise yields only a draft, a range is written, for example `humanEffort: [3, 10]`. In either case the blog post gives one sentence of reason for the score, and for a range it also states the conditions that push the skill to the upper end.

A skill that does only repeated labour, such as a skill that only sets up a React stack, usually scores between 0 and 3. Once design is involved, the score is usually 7 or above. An exploratory skill usually scores 10.

### modelIQ

`modelIQ` gives the tier of model a skill needs. Which models were tested, and with what results, is written in the blog post, because the models that belong to each tier change every few months. When the same skill runs on a weaker model, its `humanEffort` usually rises as well, and the blog post should say so.

### useCases

`useCases` is chosen only from the following list.

- `weekly-report`: a report written every week from the same sources, with a fixed set of figures and a passage on what has changed since the week before.
- `report`: a single report that carries one claim together with its data.
- `dashboard`: a page of figures with which a team follows its own work. A dashboard is read. The pages of a traditional SaaS product, with their navigation, forms, and settings, are operated, and they lie outside the scope of this site.
- `long-form-reading`: textbooks, papers, and blogs in mathematics and physics, read on the web, together with the figures that explain them.
- `talk-deck`: a page made to accompany a talk, turned slide by slide.
- `demo-review`: taking a large demo apart, writing a review from which a person can understand it, and refactoring it on that basis. The particular kind of demo, such as a game, a web application, or a data pipeline, is written in the blog post. Once enough skills have gathered for one kind of demo, that kind may be given a use case of its own.

Each use case may later have a page of its own. For now we keep it simple.

## Rework

Skills come from two sources: some we make ourselves, and others we take from elsewhere. A skill taken from elsewhere is always reworked, and its `README.md` states where it first came from: the repository, the commit it was taken from, the license, and what we changed. We state the origin first for ethical reasons. Rework also leaves the original license in force; donor material under a non-commercial license, for example, remains non-commercial after rework.

Rework is mainly tuning for our own needs. Some skills are too large, and we divide them by step so that the AI never runs entirely out of a person's sight. This adds to a person's burden, but we must be clear that the right to think and to review is not to be handed to the AI.

Each skill therefore needs two lines of defence, one for each of two kinds of failure.

- The first failure is using the wrong skill: the request does not fall within the skill's scope. A user might, for example, take a dashboard skill to make a single report. The `description` in `SKILL.md` prevents this by stating when the skill should not be used, so that the AI does not trigger it.
- The second failure is an unclear request: the request falls within the skill's scope, but the input is incomplete. A user might, for example, tell a weekly-report skill "make me a weekly report" without saying which data to use or which week to compare against. Unless the user's environment already holds enough context to fill the gap, the skill must contain a step that stops it from generating directly and asks the user to state the problem clearly.
