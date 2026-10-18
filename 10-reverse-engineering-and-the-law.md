# 10. Reverse Engineering and the Law

**Nothing here is legal advice, and the honest answer to most questions in this area is "it depends."** This page exists because the rules matter enough that folklore is a bad substitute. Read it, then decide whether you need a real lawyer. If a company contacts you, [LEGAL.md](../LEGAL.md) has what to do in the first week.

Everything here is US law unless stated otherwise. The EU is different in a way that matters, covered near the end.

## The one idea the whole area rests on

US copyright does not protect ideas, procedures, processes, systems, methods of operation, concepts, principles or discoveries. 17 U.S.C. § 102(b). It protects the specific expression a creator wrote.

A game's mechanics, its file formats, its network protocol, the order in which its physics steps run: those are function. Copyright does not reach them. What is protected is the particular code, art, text, music and level design.

That gap is the entire reason reverse engineering is usually lawful. You are allowed to learn how something works. You are not allowed to copy how it was written.

The corollary is the part people get wrong. **Reading is not copying, but disassembling is.** When a decompiler turns a binary into readable text, a copy is made. That copy is a copy of protected expression. It can still be fair use, but only because of the cases below.

## Black box, grey box, white box

The terminology matters because the three sit in very different legal positions.

**Black box** is testing through the interface. Input goes in, output comes out, and the internals are never looked at. No copy of the code exists anywhere, so there is no copyright question at all.

**Grey box** is partial visibility. Observing network traffic, or reading a value out of memory while the program runs, are grey box. What gets examined is data the program produces at runtime rather than its source.

**White box** is full access to the internals. The code is available and can be read end to end.

For this community, the practical ranking is clear:

> **Prefer black box wherever the question allows it.** It is the only approach that raises no copyright issue, because nothing is copied.

Working the problem through the interface means opening files in a hex editor and watching how the game reacts to changes, rather than reaching for a decompiler. A lot of modding work is black box and nobody realises it. Decompilers come into play when observation genuinely cannot answer the question, which is the condition the cases require anyway.

## What the courts actually decided

Four cases matter. All but one are from the Ninth Circuit, which covers California and Washington, where most American software work happens.

### Sega v. Accolade (9th Cir. 1992)

Accolade wanted to publish games for the Genesis and refused Sega's licence terms, which required exclusivity and kept the interface specifications secret. So Accolade bought a console and three cartridges, ran a decompiler, studied the output, and worked out the interface requirements. It then wrote its own games.

The holding, which every later case quotes:

> "Where disassembly is the only way to gain access to the ideas and functional elements embodied in a copyrighted computer program and where there is a legitimate reason for seeking such access, disassembly is a fair use of the copyrighted work, as a matter of law."

Two conditions, both required. It must be the only way to get at the functional parts, and you must have a legitimate reason.

The court also rejected Sega's trademark claim. Accolade's games triggered Sega's "PRODUCED BY UNDER LICENSE FROM SEGA" message because that was baked into the console's lockout code. The court held that was Sega's own doing and could not be held against Accolade.

One limit worth knowing. The trial court had suggested Accolade should have used a clean room. The appeals court called that clearly erroneous, because a clean room does not tell you what the interface specifications *are*. You have to disassemble to learn them, and only then does a clean room become possible.

### Atari Games v. Nintendo (Fed. Cir. 1992)

Atari deprocessed Nintendo's lockout chips, which means chemically stripping layers off silicon to read the object code under them. The Federal Circuit held that reverse engineering, "untainted by" a pirated copy of the program and necessary to understand it, is fair use.

Then it set the boundary that matters more than the permission:

> "This fair use did not give Atari more than the right to understand the 10NES program and to distinguish the protected from the unprotected elements of the 10NES program. Any copying beyond that necessary to understand the 10NES program was infringement. Atari could not use reverse engineering as an excuse to exploit commercially or otherwise misappropriate protected expression."

Atari lost. It had obtained source code from the Copyright Office without authorisation, a separate infringement, and its replacement chip reproduced instructions Nintendo had *deleted* from the original years earlier.

### Sony v. Connectix (9th Cir. 1999)

Connectix built an emulator for the PlayStation and reverse engineered the console's BIOS to do it. The court listed four ways to reverse engineer software:

1. Reading about the program
2. Observing it operate on a computer
3. Static examination of individual machine instructions
4. Dynamic examination while it runs

Methods 2 through 4 all require copying the program into RAM. The court held that this intermediate copying was fair use, because no copies of Sony's material ended up in Connectix's product. It also declined to draw a line between "studying" the code and "using" it, calling that distinction artificial.

### NEC v. Intel (N.D. Cal. 1989)

The first case where a clean room was used successfully as a defence, and the source of the mechanics below.

## The rule that will actually catch you out

Forget the fair use analysis. This is the practical one.

**Courts treat identical bugs and identical unnecessary instructions as the strongest possible evidence of copying.**

From *Atari*: "The existence of the identical unnecessary instructions in both codes is strong proof of substantial similarity." From *E.F. Johnson Co. v. Uniden Corp.*, where Uniden lost precisely on this, having "gone so far as copying the errors and unnecessary information in the program."

Why? Independent developers working from the same specification make different choices. Different variable names, different ordering, differently structured functions. Two independent implementations will not agree on which dead branch to keep.

If your code and the original share a quirk that serves no purpose, you did not both arrive at it independently. You copied.

So: do not reproduce the original's bugs, its dead code, its vestigial fields, its off-by-one errors, or its odd instruction ordering. Those are the fingerprints. Rewrite them on purpose, and write down that you did.

This matters more with an agent than with a person, because an agent working from decompiled output reproduces structure faithfully, including the parts that serve no purpose. See below.

## Clean room and dirty room

A **dirty room** is the default: one person looks at the original and writes the replacement. Having seen the expression, they cannot unsee it. Most hobby modding is dirty-room, including nearly everything in this repo.

A **clean room** splits the work across two groups with a wall between them. Per the *NEC v. Intel* analysis, a defensible clean room has three requirements:

1. **The people writing the code have no knowledge of the original code.** Not "they promise not to look." No access at all.
2. **The engineer producing the functional specification is a different person from the programmer writing the code.**
3. **All communication between the two groups passes through an independent third party**, acting as gatekeeper, who checks that no protected expression leaked across.

Documentation is what makes it evidence rather than an assertion. Preserve every communication, keep daily logs, keep drafts and working papers. The *NEC v. Intel* record ran to thousands of pages. The value of the documentation is that it proves the *denial* of access, which is the element a copyright owner has to establish.

### Why a late clean room fails

A clean room started after you are already sued does not usually work.

*Cadence Design Systems v. Avant!* (9th Cir. 1998) is the case to read. Avant! copied Cadence's code, got sued, and then tried to cure it: an independent expert reviewed the infringing portions, wrote specifications, and engineers who allegedly lacked access rewrote the code. The district court found the clean room inadequate, because Avant! "was able to take advantage of its knowledge of the functions and the basic structure of the Cadence code," and the use of Cadence's code to build the specifications "raised serious questions."

If you already know how the original works, you cannot unlearn it, and no amount of documentation convinces a court otherwise. The separation has to exist from the start.

### And it is not a magic shield

Clean room defeats a *copyright* claim about copying. It does not touch:

- **Patent.** A patent on a system can be infringed by a clean-room product that knows nothing about the patent. Sega lost in part because it held no patent on the Genesis console.
- **Contract.** Your EULA is a separate promise. Breaking it can be a breach of contract even when nothing is copyrighted.
- **DMCA anti-circumvention.** Below.
- **Trade secret**, if the material was obtained by someone under a duty of confidentiality rather than from a copy you bought.

## Doing this with an agent

This part is not in any of the cases, because agents are new. It follows from what the cases do say.

**An agent doing decompilation is structurally a dirty room.** If one session reads decompiled output and then writes the replacement, a single context contains both halves. No documentation fixes that, because the requirement in *NEC v. Intel* is absence of knowledge, and prompt discipline is a weak substitute for a different pair of humans.

What you can actually do:

**Split it across two sessions.** First session: reverse engineer, then write a functional specification in your own words, describing behaviour and interfaces rather than code. Close it. Second session, fresh context with no access to the decompiled output: implement from that specification alone. This is a poor person's clean room. It is genuinely closer to the doctrine than doing it all in one session, and it is not a legal safe harbour.

**Never ask for a transcription.** The moment the output is line-by-line equivalent to the original, the clean room is gone and you have a copy. Ask for behaviour instead. A spec that says "the world is a 4096 by 4096 heightmap, one byte per cell, sea level 64" is function. A spec that reproduces the original's struct layout and field order is expression.

**Tell the agent about the bug rule explicitly.** It will not infer it. Ask it to identify dead code and unused fields in the original and then *deliberately not reproduce them*, and say why in a comment so the intent is on the record.

**Keep the decompiler output out of your repository.** Same reasoning as game assets, one level up: it is a copy of protected expression. Your spec and your code are the deliverables. See [guide 6](06-rules-legal-and-publishing.md#the-golden-rule-no-game-files-in-your-repo).

## DMCA anti-circumvention is a separate wall

This is the most misunderstood part, and the part most likely to end a hobby project.

17 U.S.C. § 1201 makes it a separate infringement to circumvent a technological protection measure. Whether your mod is fair use turns out not to matter. **A perfectly lawful mod can be installed by an unlawful method.** Bypassing the security is its own offence, independent of any copying.

Practical consequences:

- Using a loader the publisher ships, or that the community maintains openly, is a different act from patching a check out of an executable.
- That is why the tModLoader rule in [guide 6](06-rules-legal-and-publishing.md) is framed the way it is. Add the companion app to your library. Do not remove the ownership check.
- Anti-cheat is the same category of thing. See [guide 6](06-rules-legal-and-publishing.md#online-play-and-anti-cheat).

Section 1201 is also the reason the takedown cases are so lopsided. In 2026 a Washington court entered $4.5 million against a defendant in a Nintendo Switch case, calculated as the $150,000 statutory maximum for each of 30 games. A separate 2026 ruling entered $2 million against the seller of the MIG Switch and MIG Dumper, with a permanent injunction. Earlier cases include Yuzu at $2.4 million and Gary Bowser at $14.5 million plus a prison sentence. Circumvention is treated far more seriously than ordinary copying.

## The EU is narrower

The US lets fair use excuse intermediate copying. The EU does not go that far. The Software Directive (2009/24/EC) permits decompilation only to obtain information necessary to achieve interoperability, and only where that information is not otherwise available. Making a competing product is outside it.

The UK followed suit in *Mars UK Ltd v Teknowledge Ltd* [2002], where reverse engineering a competitor's software to build a competing product was infringement, because the objective was direct competition rather than interoperability.

One EU advantage worth knowing: the Directive voids EULA terms that prohibit decompilation, so a European user cannot be contractually blocked the way a US shrinkwrap licence can. The US position is that a EULA does not override statutory fair use, but that has been litigated rather than settled.

Practically: if you are in the EU or UK and your goal is a direct competitor to the original, treat the clean-room analysis as necessary rather than helpful.

## Trademark is separate from copyright

Copyright covers the code. Trademark covers the name and any logos or distinctive assets. Different claims, different remedies, and losing one says nothing about the other. *Sega v. Accolade* is the example: the copyright claim failed and the trademark claim failed with it, because Sega's own lockout code caused the offending display.

Keep it simple: name your project your own thing, do not put the publisher's logo in your repo or README, and do not describe it in a way that suggests official endorsement. A prominent disclaimer saying it is an unofficial fan project costs nothing and is the ordinary baseline.

## If you are still unsure

Ask before you build, not after. Ask on the Discord, or ask a lawyer who does IP. The cases above turn on facts that you are better placed to establish than anyone reading a summary, and several of them went the wrong way for the party that thought they were safe.

## Sources

The statute, from the U.S. Code:

- [17 U.S.C. § 102](https://www.law.cornell.edu/uscode/text/17/102), scope of copyright protection, including § 102(b) on ideas and methods of operation
- [17 U.S.C. § 117](https://www.law.cornell.edu/uscode/text/17/117), ownership and the making of copies
- [17 U.S.C. § 1201](https://www.law.cornell.edu/uscode/text/17/1201), circumvention
- [17 U.S.C. § 512](https://www.law.cornell.edu/uscode/text/17/512), including the counter-notification elements in § 512(g)(3)

The opinions, in full text:

- [*Sega Enterprises Ltd. v. Accolade, Inc.*, 977 F.2d 1510 (9th Cir. 1992)](https://law.resource.org/pub/us/case/reporter/F2/977/977.F2d.1510.92-15655.html)
- [*Atari Games Corp. v. Nintendo of America Inc.*, 975 F.2d 832 (Fed. Cir. 1992)](https://law.resource.org/pub/us/case/reporter/F2/975/975.F2d.832.91-1293.html)
- [*Sony Computer Entertainment, Inc. v. Connectix Corp.*, 203 F.3d 596 (9th Cir. 1999)](https://law.resource.org/pub/us/case/reporter/F3/203/203.F3d.596.99-15852.html)

Secondary:

- U.S. Copyright Office, *Report on Software-Enabled Consumer Products, Part II: Interoperability and Competition*, which discusses the Sega, Atari and Connectix line of cases and the Phoenix Technologies BIOS clean room
- "NEC Corp. v. Intel: A Guide to Using Clean Room Procedures as Evidence," 10 Computer L.J. 453 (1990), the source of the three requirements above
- *E.F. Johnson Co. v. Uniden Corp.*, 623 F. Supp. 1485 (D. Minn. 1985), on identical errors as evidence of copying
- *Cadence Design Systems, Inc. v. Avant! Corp.* (9th Cir. 1998), on why a late clean room fails
- Directive 2009/24/EC of the European Parliament and of the Council on the legal protection of computer programs, Article 6

The enforcement figures in the DMCA section come from court documents and legal reporting rather than from the statute itself. If you need one for anything that matters, read the opinion.

---

<sub>Spot a mistake? [Edit this page on GitLab](https://gitlab.com/trevaintdead/ai-modding-guides/-/edit/main/guides/10-reverse-engineering-and-the-law.md) &middot; [Open an issue](https://gitlab.com/trevaintdead/ai-modding-guides/-/issues/new) &middot; Part of [AI Game Modding Guides](https://gitlab.com/trevaintdead/ai-modding-guides)</sub>