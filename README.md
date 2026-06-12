# OverTheWire Bandit Linux & Security Fundamentals

A documented walkthrough of the skills, concepts, and problem-solving approach I
built completing the [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
wargame end to end.

> **Note on spoilers:** OverTheWire asks players not to publish passwords or
> step-by-step solutions. In keeping with that, this repository documents the
> *skills and concepts* each stage taught not the answers. The goal here is to
> demonstrate understanding, not to provide a cheat sheet.

---

## Why this matters

Bandit is a Linux and security fundamentals gauntlet. Each level locks the next
behind a small challenge that forces you to actually use a tool or concept rather
than just read about it: SSH internals, file handling, the shell, networking,
privilege escalation, version control, and restricted-shell escapes.

I worked through it carving out focused time daily while building hands-on SOC
projects in parallel. Completing it gave me a working command of the Linux and
offensive-security primitives that underpin day-to-day blue-team work the same
techniques an analyst needs to recognise when they appear in logs and alerts.

---

## Skills demonstrated

| Skill area | Concepts practised |
|---|---|
| **SSH & remote access** | Key-based authentication, custom ports, non-interactive command execution, host-key handling |
| **File analysis** | Comparing file versions, locating data by attribute, working with awkward filenames and file types |
| **Networking** | Port and service discovery, connecting to raw TCP services, working with TLS-wrapped sockets |
| **The shell** | Variable expansion, scripting loops, understanding how the shell parses and executes input |
| **Privilege escalation** | setuid binaries, the difference between real and effective user IDs, using controlled binaries to act as another user |
| **Scheduled tasks** | Reading and reasoning about cron jobs, exploiting writable script paths, understanding execution context |
| **Version control security** | Recovering secrets from commit history, branches, and tags; understanding `.gitignore` limitations |
| **Restricted-shell escapes** | Breaking out of pager-based and input-mangling shells using legitimate program features |
| **Automation** | Generating and piping large input sets to brute-force a constrained search space |

---

## What each stage taught me

Grouped by theme rather than by level, and kept spoiler-free.

### Remote access and SSH
Bandit makes you live in SSH. I worked with private-key authentication, learned
why a service reachable *on* a host isn't the same as one you can log into *from*
the outside, and used non-interactive SSH to run a single command when an
interactive shell was deliberately sabotaged. The recurring lesson knowing
*where* a connection originates and *where* the service actually lives came up
again and again.

### Files and the filesystem
Several stages are about locating the right data: comparing two versions of a file
to find what changed, finding a file by an attribute like size or owner, and
handling filenames designed to break naive commands. These are the unglamorous
skills that make real log and artefact triage faster.

### Networking
I used port scanning to map which services were listening, then connected to those
services directly including ones wrapped in TLS, where a plain connection won't
work. Understanding the difference between a raw TCP service and an encrypted one,
and knowing the right client for each, is foundational network knowledge.

### Privilege escalation
A major theme. I worked with setuid binaries and learned the key distinction
between a process's *real* and *effective* user the mechanism that lets a program
run with its owner's permissions. Recognising a setuid binary in a file listing and
understanding why it's a privilege-escalation vector is directly relevant to both
attacking and defending Linux systems.

### Scheduled tasks (cron)
A run of stages built on cron. I learned to read scheduled jobs, reason about the
*context* a job runs in (which user, with which permissions), and understand why a
writable script path executed by a privileged job is dangerous. The most important
insight was about execution context what identity a scheduled task actually runs
as, versus who triggers it.

### Version control as an attack surface
A full sequence on Git, and some of the most job-relevant material in the game.
Secrets don't only live in the current files they hide in **commit history**,
in **non-default branches**, and in **tags**. I practised recovering credentials
that had been "removed" but never truly deleted, and saw first-hand why
`.gitignore` is not a security control. This is exactly the surface that tools
like `gitleaks` and `trufflehog` exist to scan, and it's a real concern in SOC,
IR, and pentest work.

### Restricted-shell escapes
Two stages drop you into deliberately crippled shells. Escaping them meant
understanding how the shell parses input and using a legitimate feature of the
environment to reach a normal shell. Pentesters break out of restricted shells
constantly, and analysts need to recognise the resulting artefacts so seeing the
mechanics from the inside is valuable on both sides.

### Automation under constraints
One stage requires guessing a value from a small, finite space. Rather than trying
by hand, I generated the full set of possibilities programmatically and piped them
to the service the same "make the computer do the repetitive part" instinct that
underpins detection engineering and tooling work.

---

## How I approach being stuck

The most useful thing Bandit taught me wasn't a command — it was a way of working
when the obvious path has already failed.

One stage took me **two full days**. Every documented approach was correct, yet
nothing worked. The breakthrough came from a shift in framing: I stopped assuming
the *puzzle* was the obstacle and started questioning my *environment*. The wall
turned out to be a tooling problem, not a knowledge problem. Once I isolated that
variable changing where and how I was running the commands the solution that
had been "correct" all along finally landed.

That loop is the same one a SOC analyst runs when an alert fires and the answer
isn't obvious:

- **Separate the problem from the tooling.** Is the thing failing because my
  approach is wrong, or because something in my environment is interfering?
- **Isolate variables.** Change one thing at a time so you actually learn from
  each attempt instead of guessing.
- **Know when to change the environment, not the approach.** Sometimes the fix is
  a different machine, a different shell, or a cleaner starting state not a
  different command.
- **Stay with it.** The willingness to sit in "I don't know yet" without closing
  the terminal is, in practice, the job.

I document the stuck moments deliberately. Showing only the clean wins hides the
part of learning that actually builds capability.

---

## Skills mapped to blue-team relevance

| Bandit skill | Why it matters for a SOC analyst |
|---|---|
| setuid / privilege escalation | Recognising privesc techniques and the artefacts they leave |
| Reading cron jobs and execution context | Understanding persistence and scheduled-task abuse |
| Secrets in Git history / branches / tags | Auditing repositories for leaked credentials |
| Restricted-shell escape recognition | Spotting attacker breakout behaviour in shell activity |
| Port and service discovery | Understanding reconnaissance from the defender's side |
| TLS vs. raw service connections | Reasoning about encrypted vs. plaintext traffic |
| Scripting and automation | The foundation of detection engineering and tooling |

---

## Tools used

`ssh` · `nc` (netcat) · `openssl` · `nmap` · `diff` · `git` · `cron` · core
GNU/Linux utilities · standard shell scripting

---

## Status

**Completed all levels.** Bandit is the foundational wargame in the OverTheWire
series; finishing it establishes the Linux and security fundamentals that the more
advanced wargames (and real blue-team work) build on.

---

*Part of my ongoing cybersecurity portfolio. I document my learning publicly —
including the parts that don't come easily because that's what the work actually
looks like.*
