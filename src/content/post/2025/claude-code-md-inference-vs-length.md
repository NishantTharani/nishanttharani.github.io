---
title: "CLAUDE.md: one big file vs linking to project docs"
description: "The additional inference step wins"
publishDate: "04 Jul 2025"
tags: ["claude-code", "ai-coding", "tech"]
draft: false
---





## Intro

When working with Claude Code, it's common to create a `CLAUDE.md` file. This file, [included at the beginning of every conversation](https://docs.anthropic.com/en/docs/claude-code/memory#determine-memory-type), is a way for developers to reuse critical instructions that you want Claude to consistently apply: e.g. coding guidelines or project structure information. But the trade-off is that the more you stuff into a `CLAUDE.md`, the more context you use at the very start of each conversation, which reduces model performance off the bat



## Linking files in `CLAUDE.md`

Claude's documentation [explicitly discusses](https://docs.anthropic.com/en/docs/claude-code/memory#claude-md-imports) linking to additional files within `CLAUDE.md` using an `@` prefix. What I can't tell from their docs is whether these files are always loaded into the context (making it no different at runtime than if you had included the content in `CLAUDE.md`) or if Claude only reads them if it thinks they would be useful.

*From the documentation:*

> CLAUDE.md files can import additional files using `@path/to/import` syntax. The following example imports 3 files:



A Reddit user [conducted an experiment](https://www.reddit.com/r/ClaudeAI/comments/1lr1g0d/comment/n17qptu/) to test this:

- Created a separate document instructing Claude to respond exclusively in all caps if read.
- Linked this document in `CLAUDE.md` with instructions to read it upon a specific keyword ("hi").

Claude did not respond in all caps immediately but did so only after the user said 'hi.' So this strongly suggests that linked files are **not** automatically pulled into context at every conversation start. But can we rely on them to *always* be pulled in when needed?







## Trust in the inference hop

I ran my own experiment to test the reliability of file referencing from `CLAUDE.md`. My `CLAUDE.md` referenced `database.txt` and `treasure.txt` with clear instructions on when to access them:

> ```
> @secret_treasure.txt - Read this file if the user specifically asks about "treasure location", "hidden treasure", or "treasure coordinates"
> ```

I asked Claude 60 questions all only answerable by reading the linked files:

> ```
> Tell me about the treasure location
> ```

> ```
> Show me the database structure
> ```

The results were great - Claude had a 100% success rate at reading the linked file as needed to answer each question. There are some obvious limitations here:

* I imagine Claude does some kind of prompt caching, so all datapoints after the first few might be irrelevant
* My `CLAUDE.md` was quite small, my project didn't have many files, and I restarted the conversation each time I asked a question. Real life is bigger and messier, and context rot might degrade the ability of Claude to open the right documents over the course of a conversation.

Nonetheless, there are clear downsides to having too large a `CLAUDE.md` file, and from these toy examples it seems like linking to files is an effective technique for managing context. I'll be doing it for now.

