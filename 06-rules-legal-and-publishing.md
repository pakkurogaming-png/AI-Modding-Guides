# 6. Rules, Legal, and Publishing

This is not legal advice. It's what the Discord requires and what the example projects do, from the build rules down to the posting checklist at the bottom of this guide.

## The golden rule: no game files in your repo

Your repository holds **your code only**. Never commit:

- game assets (models, textures, sounds, fonts, maps, shaders)
- game files or folders
- decompiled code or Ghidra databases
- files extracted from the game
- Minecraft assets (they come from the player's own copy at runtime)

Players supply their own copies. The example projects handle this in a few ways:

- **Setup that builds from the player's copies.** GTA San AnSkateas ships a setup script that builds what the mod needs from the player's own installs.
- **Extractor tools.** gang-beasts-rust has Python tools that read the player's install and write extracted data to a folder that Git ignores. Ask your agent to write one of these for you.
- **Reading at runtime.** IW4L and benilla read the game's files in place and never copy them.

### Use a whitelist `.gitignore`

A normal `.gitignore` lists what to leave out. A **whitelist** `.gitignore` ignores everything and lists only what to include. That way an extracted file can never be committed by accident. gang-beasts-rust does this. Ask your agent to set it up on day one.

The template in [`AGENTS-starter.md`](../templates/AGENTS-starter.md) includes this as a hard rule.

## Single-player and offline only

- **Don't inject code into an online client.** Kernel and user-mode anti-cheat are a stop sign: Easy Anti-Cheat, BattlEye, Vanguard, EA Javelin, Ricochet, ACE, nProtect, XIGNCODE and mhyprot. You can get banned. Members report that Claude won't help circumvent anti-cheat, and the universal-modder toolkit also limits itself to single-player or offline games and stays away from anti-cheat.
- If your game has an online mode, work in its single-player or offline mode only.
- Tools running next to a protected game can trip its anti-cheat even when you never touch it. Close the game before a reverse engineering session.

## Don't automate the person's keyboard

This one gets forgotten because it's not about the game's rules, it's about whoever is sitting at the machine.

Automation that drives mouse and keyboard takes over their input. Ask before starting a long automated session while they're at the PC, and check the window is idle first.

Ask before any of these, too: installing a loader into a game folder, changing registry or graphics settings, deleting anything, or publishing on their behalf.

And don't kill processes with a wildcard matcher. `pkill -f` matches your own shell. Kill by exact process ID.

This is the rule people break most often, usually by accident. If someone asks you to add an online game's content to a project, that's the line. See the "Ideas that don't work" table in [guide 2](02-passthrough-mods.md).

## Reverse engineering: what's fine and what's not

Reverse engineering is a normal part of this work, and [guide 3](03-rust-rewrites-and-ports.md) covers the tooling. The law behind these lines is in [Guide 10](10-reverse-engineering-and-the-law.md), and what to do if a publisher contacts you is in [LEGAL.md](../LEGAL.md). The lines are:

**Fine:**

- Studying a game you own, on your own machine, for your own use
- Using decompilers and format documentation to understand file formats
- Publishing your *findings* as documentation, which is how OpenMW and OpenRCT2 exist
- Building extractors so other players read their own copies
- Extracting a game from your own disc or your own dump, so a version-matching tool can reach the build it needs

**Not fine:**

- Getting around DRM, activation, or copy protection
- Circumventing anti-cheat
- Redistributing extracted assets, decompiled source, or game data
- Downloading an ISO or dump from a file-sharing site
- Making a tool whose purpose is to bypass access controls

A version downgrader sits on the fine side. It exists so a copy you own reaches the build a mod was written against, and it does nothing to the protection on the disc.

The general principle: when a loader checks that you own the game, satisfy the check the intended way. tModLoader refuses to start unless its free companion app is in your Steam library, and the answer is to add that app, not to patch the check. If a tool only works by disabling DRM or defeating an ownership check, that's the line.

There is a legal reason this rule is drawn where it is, and it is worth knowing because it covers more cases than the tModLoader example. Circumventing a technological protection measure is a **separate** infringement from copying, and it stands on its own. A mod can be entirely lawful and still unlawful to install if getting it in means getting past protection. Using a loader the publisher ships, or that the community maintains openly, is a different act from patching a check out of an executable.

**One more rule that catches people out**, and it is not about DRM at all. Courts treat **identical bugs and identical dead code** as the strongest available evidence that you copied rather than wrote independently. Two people implementing the same specification do not independently arrive at the same broken edge case. If your code and the original share a pointless quirk, you copied it. Rewrite those on purpose and leave a comment saying why.

That one matters more with an agent than with a person, because an agent working from decompiled output reproduces structure faithfully, pointless parts included. [Guide 10](10-reverse-engineering-and-the-law.md#doing-this-with-an-agent) covers how to prompt around it.

Takedowns happen even without shipping assets. Take-Two had GitHub remove re3 and reVC, the reverse-engineered GTA III and Vice City code, and later sued the authors. Activision sent a cease-and-desist to the H2M mod the day before its launch. Garry's Mod removed twenty years of Nintendo-related Workshop content after a takedown request. Keeping game files out of your repo is necessary, not sufficient.

If that happens to you, there is a formal route rather than just deleting and hoping, and it has deadlines and perjury attestations in it. Read it in [LEGAL.md](../LEGAL.md#if-your-repository-gets-a-takedown) before you reply to anyone, and get a lawyer before you file anything.

If you're unsure where a line is, ask. Nobody gets in trouble for asking first.

## Online play and anti-cheat

Single-player and offline, always. Two things worth being precise about, because both come up:

- **Anti-cheat isn't a blanket ban.** Some games with anti-cheat allow modding offline through the game's own option. Rocket League is the common example: Easy Anti-Cheat is required for online play and mods don't run while it's on, but with it off, offline matches, training, LAN, and replays work with mods. Anything online is still out.
- **Never publish anything that helps someone bypass anti-cheat.** Not a tool, not a config, not instructions.

## Credit and licenses

- **Credit every project you build on or learn from.** FalloutCraft and OWCraft both credit SkyCraft by name and keep its license.
- **Keep their licenses.** Add a `THIRD-PARTY-NOTICES.md` listing what you reused and under which license (OWCraft does this).
- **Pick a license for your own code.** MIT is common among these projects. Without a license, others can't legally reuse your code.
- **Say it's an unofficial fan project** and not affiliated with the game's developer or publisher.
- **Say you used AI.** Several example projects have an honest note about it. It helps people judge the project and trust it. Undisclosed AI-generated releases get reacted badly to, and some communities ban them outright.
- **Say what's finished and what isn't.** Test before you claim something works.
- **Don't ship decompiler output.** Names like `FUN_` or `sub_` in a released mod tell a reviewer the code was decompiled rather than written. Rename them.
- **Keep retail offsets out.** A hardcoded address from the original executable does nothing in your own build and advertises where the code came from.

If a rights holder asks you to change or remove something, do it. gang-beasts-rust says this in its README, and it's the right default whether or not another project bothers to.

## Posting your project

You've got something that runs. Now you want people to find it, and you don't want your project taken down over a rule you didn't know about.

Post to **#share-your-projects** on the Discord so the right people see it. Half-working projects are welcome there, not only finished ones. Put your project on **GitHub** and link the repo if you want other people to be able to use it. A repo is strongly recommended. A direct download link isn't. Use the tags and the post template from the pinned guidelines post.

### Never post these

- Ripped assets, textures, models, sounds, or maps
- Leaked or decompiled game code
- Game files of any kind
- Links to pirated or leaked material
- Direct file hosts or download links instead of a repo

If your project needs game content, it reads it from the player's own install. That's the rule, and it's why every example project has an extractor or a setup script instead of a data folder.

## Before you post: the pre-flight

Go through this list. It takes five minutes and prevents the problems that come up most.

- [ ] `git status` is clean and there's no uncommitted game data
- [ ] Repo is scanned for large files: `git ls-files | xargs du -h | sort -rh | head -20`
- [ ] `.gitignore` is a **whitelist**, ignoring everything and including only source
- [ ] `git log --all --stat` shows no assets were ever committed
- [ ] You credited every project you built on, with a link
- [ ] `THIRD-PARTY-NOTICES.md` exists if you reused code
- [ ] README states the games and **exact versions** it needs
- [ ] README states what works and what doesn't
- [ ] README says it's an unofficial fan project
- [ ] README mentions you used AI
- [ ] Any release zip was checked for game files
- [ ] It was tested on a clean machine, not only your own

Two of those catch the most common problems. `git log --all --stat` is the one people skip, and it's the only way to find an asset that got committed three weeks ago and then deleted. `git ls-files | xargs du -h | sort -rh | head -20` catches a 400 MB texture someone added before setting up the whitelist.

### If you already committed game files

Git history is public the moment you push. Do this:

1. Rotate anything sensitive first.
2. `git filter-repo --path path/to/bad --invert-paths`
3. Force-push: `git push --force`
4. Ask GitHub Support to garbage-collect the old objects. They only become unreachable, not gone, until then.
5. Delete and re-upload any release zip that contained them.

Assume anything ever pushed was copied. A history rewrite alone doesn't remove it from anyone who already cloned, so don't rely on it alone.

## The README is the post

Most people read the README and nothing else. Structure yours like this:

```markdown
# [Project Name]

One sentence: what it does and what makes it different.

![Screenshot or short GIF](docs/screenshot.png)

## What works
- Feature
- Feature
- Feature

## What doesn't work yet
- Feature that's half-done
- Anything untested
- Known bugs

## Requirements
- Game A: version X.Y.Z (Steam / GOG / other)
- Game B: version X.Y.Z
- [Loader](link) for Game A
- Single-player / offline only

## How to install
1. Install both games and the loader.
2. Build: `setup.ps1` or `./gradlew build`
3. Copy the output into [folder].
4. Run Game A.

## How to play
Short, concrete steps.

## How it works
A few paragraphs, or link docs/DESIGN.md.

## Credits
- [SkyCraft](link): the design this is based on
- [Everyone else who helped]

## Legal
Unofficial fan project. Not affiliated with or endorsed by the publisher.
No game assets are included. Players supply their own copies.

Built with AI coding agents.
```

## Make the repo look trustworthy

New projects with no stars and no commits get ignored. These things help:

- **A screenshot or a GIF.** Usually the difference between a click and a scroll. Notepad-draw something if you have to.
- **A commit history.** Twenty small commits reads as "someone who works carefully." One giant commit reads as "paste."
- **A MODLOG.md.** Shows you test what you claim. OWCraft keeps one and links it from its README, which is the cheapest possible signal that you actually test.
- **An honest "what doesn't work" section.** This builds more trust than a list of features, and it saves you the support questions.
- **Tell people to back up their saves.** The early projects in this space all say it, and they're right.

## The forum post

Keep it short. The README does the detail.

```
**Title:** [Game A] + [Game B] passthrough mod

**Games:** [Game A] v[version] + [Game B] v[version]
**Repo:** [link]
**Needs:** both games installed, plus [loader] for [Game A]

**What works:** [2-3 bullets]

**What's broken:** [be honest]

**Tested on:** Windows [version], GPU [model]
**Tested how:** [installed on a clean machine / only on my own PC]
```

The last line matters more than it looks. Saying you only tested on your own machine tells the reader exactly how much to trust the post, and it saves you a support thread where someone discovers a problem you could have warned them about.

### Tags and format

Use the tags and the post template from the pinned guidelines on the Discord. If you skip the template your post is harder to read and gets less help.

### After you post

- Stay in the thread. Most "it doesn't work" reports are a version mismatch or a missing loader, and you can answer in one line.
- Ask for logs, not descriptions: "I need your log from both games, not what it looked like."
- When someone reports it works for them but not for you, that is a bug report worth chasing.

## Where else to publish

- **GitHub Releases:** ship a build as a zip. Check the zip contents for game files first. Many of these projects do this.
- **Nexus Mods / ModDB:** external mod sites with their own rules. Most require that users supply their own game files.
- **Steam Workshop:** if your game supports it. Same rule: no copyrighted game content in your upload. Ask your agent to write an extractor players run themselves rather than shipping assets.
- **A thread on the Discord.** Link the repo. Don't repost the whole thing.

## If a rights holder contacts you

Remove it. That's the right call whether or not another project bothers to say so in its README, and gang-beasts-rust does. Credit and links to the original work help, but they aren't a licence to keep shipping someone's assets. There is a formal takedown route with deadlines in it — read it in [LEGAL.md](../LEGAL.md#if-your-repository-gets-a-takedown) before you reply to anyone, and get a lawyer before you file anything.

## The post-checklist

The pre-flight above is about repo hygiene. This one is about whether anyone reads it.

- [ ] Passed the pre-flight
- [ ] Repo link, not a download link
- [ ] Games and exact versions stated
- [ ] A screenshot or GIF
- [ ] An honest "what doesn't work" section
- [ ] Credits in the README, and on the post itself
- [ ] Used the Discord template and tags
- [ ] Said it's an unofficial fan project
- [ ] Mentioned AI use
- [ ] Told people to back up their saves
- [ ] Ready to answer questions in the thread

---

<sub>Spot a mistake? [Edit this page on GitLab](https://gitlab.com/trevaintdead/ai-modding-guides/-/edit/main/guides/06-rules-legal-and-publishing.md) &middot; [Open an issue](https://gitlab.com/trevaintdead/ai-modding-guides/-/issues/new) &middot; Part of [AI Game Modding Guides](https://gitlab.com/trevaintdead/ai-modding-guides)</sub>