# 1. Choosing and Setting Up an AI Agent

## Agent vs. chat website

Most people get stuck on this one.

- **A chat website** (claude.ai in the browser, ChatGPT in the browser) only sees what you paste or upload. It can't open your game folders, so you hit "file too large" errors and end up copy-pasting code by hand.
- **An agent** runs on your PC. It reads your game folders, creates and edits files, runs builds, and reads logs. Nothing to upload.

You want an agent. If you're using the Claude desktop app, look for the Code mode. It is in the top left and easy to miss. Check the provider's current docs for exact steps.

## Which agent

Members mostly use these. Pick one and stick with it while you learn.

| Tool | Notes from the community |
|------|--------------------------|
| **Claude Code** | The most mentioned. Use the desktop app; the terminal and VS Code versions work fine but are worse to live in |
| **Codex** | Also widely used, including to run Ghidra-based decompiling |
| **OpenCode** | Works with many models, including free ones. Which free model is best is still an open question |
| **VS Code + Roo Code + OpenRouter** | A pay-per-use route, commonly paired with a DeepSeek model. Only worth it if a subscription is not available to you, since it costs more for the same work |

### A note on "MCP"

Several people confused these. **Claude Code and Codex are agents.** MCP (Model Context Protocol) is a standard for plugging extra tools into an agent.

You do **not** need any MCP server to start. None of the example projects list one as a requirement, and no MCP server is needed to read your game files, write code, or run a build. That is all the agent does on its own.

A command line beats an MCP server. If a tool has a usable CLI, the agent can drive it directly and you have one fewer moving part. Reach for MCP when there is no CLI worth using, which is mostly the case with GUI-only desktop tools.

If you go on to reverse engineer anything, that's when one is worth having, because a decompiler is a GUI tool with no command line worth scripting. Ghidra and IDA both ship MCP servers, which means the agent can decompile and rename functions itself instead of you pasting disassembly into a chat. The official [Hex-Rays IDA MCP](https://github.com/HexRaysSA/ida-mcp) installs with one command, and [ghidra-mcp](https://github.com/bethington/ghidra-mcp) does the same for the free option.

### What the agent is

Each of these has a desktop app and a terminal version, and you want the app. The terminal versions are what the developers test, which makes them the most current, but they are also unpleasant: a chat window you cannot resize, no inline images, and status you cannot see without typing a slash command. The apps cost nothing extra and are easier to live in for hours.

The agent runs on your machine with your user account's file access, so what protects you depends on how you've configured it.

Claude Code asks before it acts, shows file edits as diffs for you to approve, and has a built-in sandbox you switch on with `/sandbox`. Codex has its own permission and sandbox settings. Protection drops when people switch to full-access modes, which members here describe doing. That is why the safety section at the bottom of this guide matters: the defaults help, and the failure mode is turning them off.

The sandbox has real limits. It covers shell commands only, so Claude's own file tools, hooks and local MCP servers still run with your full access. It runs on macOS, Linux and WSL2, so on native Windows you need Claude Code inside WSL2 to get it. It is off by default. And if it cannot start, because a dependency is missing or the platform is unsupported, Claude Code warns you and carries on running commands unsandboxed rather than stopping. Setting `failIfUnavailable` makes it exit instead, which is the stricter choice if you want the sandbox to be a real gate.

One more thing worth knowing: when a command fails under the sandbox, Claude may retry it with `dangerouslyDisableSandbox`, and that retry runs outside the boundary. In the default modes you get a prompt unless a matching allow rule covers it, so decline it when you did not expect it. In `bypassPermissions` mode the retry runs with no prompt at all, which is one more reason not to run that way. `/sandbox` has an Overrides tab that turns the retry off, called strict sandbox mode.

Sandboxed commands can still read most of the machine by default, including credential files such as `~/.ssh` and `~/.aws/credentials`, unless you deny those paths in the sandbox settings.

None of that makes it safe. It narrows what a shell command can reach, which is worth having, and the rest still depends on the habits at the bottom of this guide.

None of the agents will touch DRM or anti-cheat on their own, and online-only games are a no-go for all of them. If an agent refuses something, read the reason before assuming it's a limitation on what it can do.

You do not need an IDE, though VS Code is worth installing so you get proper file views and diffs. The agent creates your files and runs your builds either way, so you never copy-paste code into folders by hand.

## Which model

Models change quickly, so check what's current before choosing.

- Most people doing this pair Claude Code with a mid-tier Claude model. That is the best value on this kind of work. Several use the top tier, which is worth it for long unattended runs rather than everyday iteration.
- Others use OpenAI's models through Codex.
- Cheaper models work for smaller tasks, but they need more babysitting (see the handoff trick in [guide 4](04-prompting-and-workflow.md#when-a-chat-really-is-stuck)).
- **Local models** (running on your own GPU) don't work well for this. A 12 GB GPU is not enough for a good local coding model, which answers that question for most people. If you've made it work regardless, please add a write-up.
- Your hardware (a 5090, for example) doesn't matter for cloud models. The AI runs on the provider's servers.

## Cost and usage limits

What to pay, and what to point it at. Everything here was checked against the providers' own pricing pages in October 2026. Plans change, so verify before you commit.

### The short version

| Budget | What to get | What you get |
|--------|--------------|--------------|
| Free | [OpenCode](https://opencode.ai) with a free model | Enough to try things and follow the guides |
| About $10 | [OpenCode Go](https://opencode.ai/go) | The best value. DeepSeek V4.1 Flash and friends at high volume |
| $20 | [Claude Pro](https://claude.com/pricing) | The best single subscription, and the pick if you're buying one plan |
| $40 | OpenCode Go Plus | More of the same models. Rarely the best call over Claude Pro |
| $100 | Claude Max 5x | For when $20 runs out mid-project |
| $200 | Claude Max 20x | Only once you've proven you need it |
| Pay per token | Any provider's API, usually via OpenRouter | A last resort, if a subscription is not available to you |

Reach for a different model and the price moves a lot. DeepSeek V4.1 Flash is $0.15 and $0.60 per million tokens off-peak through OpenCode Go, and a direct API comes out close to that. Claude's current flagship, Opus 5.5, is $4 and $20. Per token means you never hit a wall, and you also never get a flat monthly rate, which suits people who work in bursts.

What to expect on a plan: a short reset window (about 5 hours) and a weekly limit. Roughly 3-4 hours of constant use out of each 5-hour window on a top Claude model is a typical budget, and a mid-tier model stretches further. The $20 tier has been enough to build a from-scratch basketball game.

### About $10: OpenCode Go

Go is a $10 subscription giving access to the strongest open coding models. DeepSeek V4.1 Flash is the one to start with: cheap, fast, and good enough for most of this work.

DeepSeek V4.1 Flash through Go costs $0.15 per million input tokens and $0.60 output off-peak, doubling to $0.30 and $1.20 at peak. Peak is 01:00 to 04:00 and 06:00 to 10:00 UTC on weekdays. Run your builds overnight and you pay the off-peak rate.

Its $60 monthly allowance works out to roughly 26,000 requests per five-hour window, or 130,000 a month.

Go's limits are the same shape as Claude's: 20% of the monthly allowance per five hours, 50% weekly, 100% monthly.

Go Plus is $40 with higher limits. Only worth it if you keep hitting Go's cap and don't want to switch to Claude.

### $20: Claude Pro

The default recommendation if you're buying one subscription. Most people doing this work are on it.

$20 monthly, or $17 monthly on annual billing ($200 up front). Claude Code is included.

Limits are a rolling five-hour session window plus a weekly cap that resets at a fixed time assigned to your account. Expect roughly 3 to 4 hours of constant use per five-hour window on a top model, and more on a mid-tier one. A from-scratch basketball game came in comfortably on the $20 tier.

Treat the session window as the real constraint. Stay in one chat and let prompt caching carry the repeated context, because a long session with a warm cache costs far less than restarting it. See [guide 4](04-prompting-and-workflow.md#stick-to-one-chat).

### If you want to upgrade, go in order

**$20, max it out. Then $100, max it out. Then $200.**

The reasoning is that each tier is worth it only if you exhausted the one below.

Before upgrading, though, try a cheaper model on the same problem. A mid-tier Claude model is close to the top one on most of this work, at a fraction of the price, and if it handles your task the upgrade bought you nothing. The top tier earns its money when you are running long unattended jobs and want the result right first time, not when you are iterating with playtests.

| Tier | Price | Pro equivalent |
|------|-------|----------------|
| Pro | $20 | 1x |
| Max 5x | $100 | 5x |
| Max 20x | $200 | 20x |

Two things people get wrong here:

- **The 5x and 20x multiples apply to the five-hour session window**, not to your weekly allowance. The $200 plan's weekly allowance is roughly double the $100 plan's, not twenty times.
- **A weekly limit sits on top of the session window.** Upgrading multiplies your session capacity; it doesn't remove the weekly cap.

Max is monthly only. Upgrading mid-cycle charges prorated.

If a subscription genuinely is not available to you, the pay-per-token route is OpenRouter with a strong open model. VS Code plus Roo Code with OpenRouter and a DeepSeek model is the usual combination. Expect to pay more than a subscription for the same amount of work.

#### Two things that change the bill more than the model

Both matter more here than in most projects, because this work is context-heavy. You are feeding the agent decompiled output, whole repositories, and design docs.

**Context window is 1M tokens on both main options, at standard price.** DeepSeek V4.1 Flash has a 1M context. Opus 5.5 includes the full 1M window at the normal per-token rate, so a 900k request costs the same rate as a 9k one. There is no long-context surcharge to worry about. DeepSeek caps output at 384K.

**Prompt caching cuts the cost of repeating context by 90%.** A cache read bills at 0.1x the base input price. Writing to the 5-minute cache costs 1.25x base input, so caching pays for itself after one read. The 1-hour cache costs 2x, so it needs two reads to break even. Both DeepSeek and Claude support it.

That matters because the expensive thing in a long modding session is the same repository and the same decompiled files going back in on every turn. Cache the stable prefix and stop paying full price for it.

Ask the agent to enable it rather than assuming it is on:

```
This project has a large stable context: docs/DESIGN.md, the game format
spec, and the files we've already analysed. Enable prompt caching on that
prefix so we stop paying full input price for it every turn. Show me the
cache hit rate in the usage output.
```

**Batch API is half price** on both input and output, for work that can happen in the background. Re-compiling a batch of assets or running the same analysis over a hundred data files is the case for it. It is no use for interactive work, since the results come back later.

One caveat on Claude specifically: Opus 5.5 uses a newer tokenizer that produces roughly 30% more tokens for the same text than earlier models did. Comparing two models on cost without checking which tokenizer they use is comparing different units.

### OpenAI and ChatGPT models

Not recommended right now, for two reasons: they perform worse on this kind of work than Claude at comparable tiers, and they give less usable usage per subscription.

**If you already have a ChatGPT subscription, keep it.** The models are very good and the allowance is decent. There's no reason to cancel. Don't switch for this recommendation.

This is a community read, not a benchmark result, and it will go stale. If you disagree and get better results on a real project, that counts, and the page should change.

### Free options that aren't nothing

Before spending anything:

- **Free models inside OpenCode.** Several are free for a limited time, including DeepSeek-adjacent options and some stealth models. Free models get cut off and rate-limited, so treat them as good for learning the workflow and bad for a serious project.
- **Pay-per-token APIs.** Buy a subscription instead. The plans are heavily subsidised, so a flat monthly rate is usually cheaper than the same tokens bought one at a time, and it removes the risk of a runaway loop billing you real money. Pay per token only if you cannot get a subscription at all.
- **Cheaper Claude tiers.** Running Sonnet instead of a top model costs less per token and is usually good enough for the bulk of a project. Save the expensive model for the part where you're stuck.

### What a plan gets you that tokens don't

Subscription plans aren't only about price. Three things are easier on a plan and awkward per token:

- **No usage ceiling.** Per-token billing means a runaway loop costs real money instead of hitting a wall.
- **Priority access.** Max plans get served ahead of free users at busy times, which matters when you're mid-project.
- **Credits included.** Paid plans come with usage credits for image and asset generation, which you otherwise buy separately.

The subscription is the cheaper route for anything you keep working on, and the ceiling is what you are really buying. Per token only wins if you stop after an hour, which is not what these projects are.

### Open questions

- Which free model is genuinely best inside OpenCode. Nobody has run the comparison.
- Whether free plans can complete a real project. Most people hit limits fast.
- Whether local models can handle a real project on 12 GB of VRAM.
- Whether any of this holds on Windows vs Linux. Every project in these guides is Windows-only anyway.

If you find out, post it on the Discord or open a pull request.

### Where to check current prices

- [Claude pricing](https://claude.com/pricing) and [Max plan details](https://support.claude.com/en/articles/11049741-what-is-the-max-plan)
- [OpenCode Go](https://opencode.ai/go) and [Zen pricing](https://opencode.ai/v2/docs/console/models/)
- [DeepSeek API pricing](https://api-docs.deepseek.com/quick_start/pricing)
- [Claude API pricing](https://platform.claude.com/docs/en/about-claude/pricing), which is the fuller page and has the per-model table the marketing page hides

Subscribe to a monthly plan rather than annual until you know how much you use. The annual discount is 15% on Claude Pro, which isn't worth paying for a plan you might outgrow in a month.

## Setting up safely

Agents can read and delete files, and many members run them with broad access. That's convenient and risky at the same time. A few habits cut the risk:

1. **Make one folder for the project** and run the agent inside it.
2. **Use Git from day one.** Commit after each working step so you can undo mistakes.
3. **Back up your game saves** before testing.
4. **Be careful with "full access" modes.** Running an agent with full PC access is more convenient, but a mistake can hit files you care about. A separate Windows user account, a virtual machine, or a container such as Podman limits the damage.
5. **Keep passwords and API keys out of files the agent can read**, and out of your repo.
6. **If the agent keeps failing to get access** (a common Codex complaint), read that tool's docs on permission and sandbox settings and grant it access to your project folder and game folders only.
7. **Write down the rules instead of trusting your memory.** A rules file in your project means the agent follows them in every session, including the ones you forget. See [`templates/AGENTS-starter.md`](../templates/AGENTS-starter.md).

## Helpful extras (optional)

- **VS Code** (or another editor) with your agent's extension, so you get proper versioning and file views. It isn't required.
- **Git and a GitHub account.** You'll need these to share your project.
- **[universal-modder](https://github.com/rehan-remade/universal-modder):** an open-source toolkit that wraps an agent you already have. Clone it and start your agent inside it. It works with Claude Code, Codex, Cursor, Gemini CLI, Copilot and OpenCode. Its art tools need a separate fal API key, and it needs Python 3.10+ and ffmpeg. It limits itself to single-player or offline games you own and won't touch anti-cheat.

Worth being clear about what the "skills" in projects like this are, since the marketing leans on them. A skill is a prompt file that gets attached to your conversation every turn. It is not a plugin, it does not add capability the model lacks, and installing eleven of them does not make your agent better at anything. A skill that lists the tools and paths for your particular game saves you a search. That is the whole value, and it is real, but it is a convenience. The same knowledge typed into the chat once does the same job.

You do not need any of this to start. Tell your agent what you want and let it work.

---

<sub>Spot a mistake? [Edit this page on GitLab](https://gitlab.com/trevaintdead/ai-modding-guides/-/edit/main/guides/01-choose-and-set-up-an-ai-agent.md) &middot; [Open an issue](https://gitlab.com/trevaintdead/ai-modding-guides/-/issues/new) &middot; Part of [AI Game Modding Guides](https://gitlab.com/trevaintdead/ai-modding-guides)</sub>