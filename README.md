# OverTheWire Bandit

A walkthrough of the Linux and security fundamentals I built completing [OverTheWire Bandit](https://overthewire.org/wargames/bandit/) end to end.

**On spoilers:** OverTheWire asks players not to publish passwords or step by step solutions. This documents the skills and concepts each stage taught, not the answers. The point is to demonstrate understanding, not to hand out a cheat sheet.

## At a Glance

| Field | Detail |
| --- | --- |
| Wargame | OverTheWire Bandit, all 34 levels |
| Focus | Linux fundamentals, SSH, privilege escalation, Git, restricted shells |
| Tools | ssh, nc, openssl, nmap, diff, git, cron, GNU coreutils |
| Cadence | Daily, alongside hands on SOC lab work |
| Status | Complete |

## Why This Matters

Bandit locks each level behind a challenge that forces you to actually use a tool rather than read about it. SSH internals, file handling, the shell, networking, privilege escalation, version control, restricted shell escapes.

The blue team value is not that I can escalate privileges. It is that I have seen every one of these techniques from the inside, which is what makes them recognisable in a log.

An analyst who has never used a setuid binary reads `-rwsr-xr-x` in a file listing and sees a permission string. An analyst who has used one sees a privilege escalation vector.

## Skills

| Area | Concepts |
| --- | --- |
| SSH and remote access | Key based auth, custom ports, non interactive execution, host key handling |
| File analysis | Comparing versions, locating data by attribute, handling filenames built to break naive commands |
| Networking | Port and service discovery, raw TCP, TLS wrapped sockets |
| The shell | Variable expansion, loops, how the shell parses and executes input |
| Privilege escalation | setuid binaries, real versus effective UID, using controlled binaries to act as another user |
| Scheduled tasks | Reading cron, reasoning about execution context, writable script paths |
| Version control | Recovering secrets from commit history, branches, and tags. Why .gitignore is not a control |
| Restricted shells | Escaping pager based and input mangling shells using legitimate program features |
| Automation | Generating and piping input sets to brute force a constrained search space |

## What Each Theme Taught

### SSH

Bandit makes you live in it. Private key auth, non interactive execution when the interactive shell is deliberately sabotaged, and the recurring lesson underneath all of it: a service reachable *on* a host is not the same as one you can log into *from* outside.

Knowing where a connection originates and where the service actually lives came up in almost every stage. That distinction is the same one that separates an inbound probe from a resident beacon in a packet capture.

### Files

Comparing two versions to find what changed. Finding a file by size or owner. Handling filenames engineered to break commands that assume well behaved input.

Unglamorous, and it is most of what artefact triage actually is.

### Networking

Port scanning to map what is listening, then connecting to those services directly, including ones wrapped in TLS where a plain connection returns nothing useful.

Knowing which client a service needs, and why a raw TCP connection to a TLS port fails, is foundational. It is also why plain HTTP on port 443 is worth alerting on.

### Privilege Escalation

The distinction that matters: **real UID versus effective UID.**

A setuid binary runs with its owner's permissions rather than the caller's. That is the whole mechanism, and it is why a single misconfigured binary hands an attacker root.

Recognising setuid in a file listing, and understanding why it is a vector rather than a curiosity, applies directly to both sides of a Linux host.

### Cron

Several stages built on it, and the useful insight was not the syntax.

It was **execution context.** Which identity does the job run as, versus who scheduled it. A writable script path executed by a privileged job is not a misconfiguration, it is a privilege escalation waiting for someone to notice.

That is also why cron is one of the most abused persistence mechanisms on Linux. It is built in, it survives reboot, and it looks like housekeeping.

### Git as an Attack Surface

The most job relevant sequence in the game.

Secrets do not only live in the current files. They live in **commit history**, in **non default branches**, and in **tags**. I recovered credentials that had been removed from the working tree and never actually deleted.

`.gitignore` is not a security control. It stops a file being added. It does nothing about the file you already committed three months ago.

This is exactly the surface gitleaks and trufflehog exist to scan, and it is a live concern in SOC, IR, and pentest work alike.

### Restricted Shell Escapes

Two stages drop you into deliberately crippled shells. Escaping meant understanding how the shell parses input, then using a legitimate feature of the environment to reach a normal one.

The defender's side of this is the point. An analyst needs to recognise the artefacts a breakout leaves, and seeing the mechanics from the inside is what makes them legible.

### Automation

One stage requires guessing a value from a small finite space. Rather than trying by hand, generate the full set and pipe it in.

Same instinct that produces detection tooling: make the computer do the repetitive part.

## The Two Day Wall

The most useful thing Bandit taught me was not a command.

One stage took two full days. Every documented approach was correct. Nothing worked.

The breakthrough came from a change of frame. I stopped assuming the puzzle was the obstacle and started questioning my environment. It was a tooling problem, not a knowledge problem. Once I isolated that variable, changing where and how I was running the commands, the solution that had been correct the whole time landed immediately.

That loop is the one a SOC analyst runs when an alert fires and the answer is not obvious:

**Separate the problem from the tooling.** Is this failing because my approach is wrong, or because something in my environment is interfering?

**Isolate variables.** Change one thing at a time, so each attempt teaches you something instead of adding noise.

**Know when to change the environment, not the approach.** Sometimes the fix is a different machine or a cleaner starting state, not a different command.

**Stay with it.** The willingness to sit in "I do not know yet" without closing the terminal is, in practice, the job.

The same shape shows up in my labs. A UFW rule that was syntactically correct and did nothing, because the traffic never passed through the chain the rule sits in. The rule was not wrong. The environment was not what I assumed.

I document the stuck moments deliberately. Showing only the clean wins hides the part that builds the capability.

## Blue Team Relevance

| Bandit skill | Why it matters in a SOC |
| --- | --- |
| setuid and privilege escalation | Recognising the technique and the artefacts it leaves |
| Cron and execution context | Understanding persistence and scheduled task abuse |
| Secrets in Git history, branches, tags | Auditing repos for leaked credentials |
| Restricted shell escapes | Spotting breakout behaviour in shell activity |
| Port and service discovery | Reading reconnaissance from the defender's side |
| TLS versus raw connections | Reasoning about encrypted and plaintext traffic |
| Scripting and automation | The foundation of detection tooling |

## Tools

ssh, nc, openssl, nmap, diff, git, cron, GNU coreutils, shell scripting

## Status

All levels complete.

Bandit is the foundational wargame in the OverTheWire series. Finishing it establishes the Linux and security primitives the more advanced wargames build on, and that blue team work assumes you already have.

---

[![LinkedIn](https://img.shields.io/badge/LinkedIn-WilliamInCyber-blue?style=flat&logo=linkedin)](https://linkedin.com/in/WilliamInCyber)
[![X](https://img.shields.io/badge/X-@WilliamInCyber-black?style=flat&logo=x)](https://x.com/WilliamInCyber)
[![Medium](https://img.shields.io/badge/Medium-@wgokahp-black?style=flat&logo=medium)](https://medium.com/@wgokahp)
