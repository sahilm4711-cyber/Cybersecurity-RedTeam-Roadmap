# Cybersecurity Red Team Roadmap

## A specific, simple roadmap to become a Red Team professional — beginner to advanced

This repo is **not** a general cybersecurity notes collection.
It is a **specific Red Team roadmap**: what to learn, in what order, why that order matters, which tools to learn, how to practise safely, and what proof you should have after each phase.

Written in simple English, so anyone can follow it — even if you are starting from zero.

---

## Who is this for?

**This is for you if:**
- You want to become a Red Team professional, not just collect certificates.
- You are ready to learn Linux, networking, web, and systems step by step.
- You want to practise in labs and build real proof: scripts, audit reports, and write-ups.
- You can follow one phase at a time, without jumping to advanced hacking on day one.

**This is NOT for you if:**
- You want quick tricks to hack real websites, accounts, or networks. That is not here, and it is not allowed.
- You want to skip fundamentals and go straight to tools. Tools without understanding do not make a professional.

---

## The one safety rule — read this first

> **Practise only on systems you own, or systems you have written permission to test.**
>
> That means:
> - Your own Kali Linux virtual machine.
> - Your own lab network, your own test website, your own test files.
> - Legal lab platforms made for learning, like TryHackMe, HackTheBox Academy, and PortSwigger Academy.
>
> **Never:**
> - Scan, test, or attack a real website, company network, school network, or another person's computer without written permission.
> - Use real passwords, real personal data, or real phishing emails in practice.
> - Share private details from a lab report in public. If a report contains your hostname, username, or local ports, think before you publish it.
>
> Every example in this repo follows this rule. Use test passwords only, like `hello123`. Use sample files you create yourself.

If you are ever unsure whether something is allowed, stop and ask: "Do I own this system, or do I have written permission?" If the answer is no, do not do it.

---

## How to use this roadmap — the learning algorithm

Do not read this repo like a book from start to end in one day. Use this loop for every topic:

```
LEARNING ALGORITHM — for every topic in this roadmap:

1. UNDERSTAND in simple words
   - Read the concept file.
   - Say it back in your own words, in one or two sentences.
   - If you cannot explain it simply, read it again slowly. Do not move on yet.

2. SEE one example
   - Look at the lab example in the file.
   - Understand what input goes in, and what output comes out.
   - Ask: "What changed? Why did it change?"

3. DO it in your own lab
   - Open your own Kali VM or lab platform.
   - Run the safe example yourself, with test data only.
   - Do not copy-paste blindly. Type it, run it, watch the output.

4. BREAK it down
   - Change one small thing and run it again.
   - Note what breaks, what stays the same, and why.
   - This is how logical thinking grows: inputs, trusted parts, failure points.

5. WRITE notes in your own words
   - Use the note template in 01-roadmap/how-to-learn.md.
   - Write: Concept, How it works, Lab steps, Output meaning, Mistakes, Questions.
   - If your notes are only copied text, they are not your notes yet.

6. EXPLAIN it in English
   - Record a short 2 to 5 minute explanation in simple English.
   - Explain what you learned, what you did, and what the output means.
   - This builds both red-team logic and communication skill, which employers check.

7. REVIEW next day
   - Next morning, re-read yesterday's notes for 10 minutes.
   - Try the lab example again without looking at the notes first.
   - If you can do it again, you really learned it. If not, repeat steps 1 to 5.

REPEAT this loop for the next topic. Do not jump phases.
```

**Practical design note — why this loop works:**
Watching videos alone feels like learning, but it fades fast. Doing, breaking, writing, and explaining creates memory. A professional hacker is not someone who knows 100 tool names. A professional is someone who can look at a system, understand how it works, find where it can fail, test it safely in a lab, and explain the finding clearly in a report. This loop trains exactly that skill, one small step at a time.

---

## Roadmap at a glance — beginner to advanced

Read the full design in `01-roadmap/overview.md`. This table is only the map, not a shortcut. Each phase has its own detailed file coming in later commits.

| Phase | Focus | Rough time* | Main skills | Main tools (concept first, lab second) | Proof you should have |
|-------|-------|-------------|-------------|------------------------------------------|------------------------|
| 0 | Mindset, ethics, lab setup | 1 week | Safety, lab setup, note-taking | Kali VM, VirtualBox/VMware, terminal | Working lab + first notes page |
| 1 | Computer and Linux fundamentals | 3-4 weeks | OS, files, processes, permissions, filesystem | Terminal, ls, cat, grep, chmod, find | Linux notes + permission checks |
| 2 | Networking fundamentals | 3-4 weeks | IP, DNS, ports, TCP/UDP, HTTP, packets | ping, ss, ip, dig, Wireshark (lab only) | Network notes + localhost port check |
| 3 | Web fundamentals and OWASP | 4-6 weeks | How websites work, requests, cookies, sessions, OWASP Top 10 in simple words | Browser dev tools, Burp (lab only), curl | Web notes + lab write-ups |
| 4 | Systems and Active Directory basics | 4-6 weeks | Windows/Linux systems, users, services, AD concepts | PowerShell basics, AD lab (own lab only) | System audit report + AD notes |
| 5 | Red Team tools and techniques | 6-8 weeks | Recon, initial access in lab, privilege escalation in lab, persistence concepts, defence evasion concepts | Nmap, ffuf, Metasploit (lab only), BloodHound (lab only) | Lab write-ups with findings and fixes |
| 6 | Red Team operations, reporting, job prep | 4-6 weeks | Planning a lab engagement, reporting, communication, portfolio, interviews | Report template, GitHub portfolio, resume | 2-3 polished write-ups + portfolio |

\* Time is flexible. It depends on your daily time, college, and practice. Do not rush a phase to meet a date. Move on only when you can pass that phase's checklist in `01-roadmap/overview.md`.

**Total path:** Roughly 9 to 12 months of steady work to become genuinely employable, if you practise regularly and build proof. A high salary in 12 months from zero is a stretch target, not a normal fresher outcome. The honest goal here is: real skills + real proof + clear communication, which is what gets interviews.

---

## Folder structure — how this repo will grow (15 to 20 big commits)

This repo is built slowly, one big commit at a time. No shortcuts, no tiny files with only a tool name.

```
Cybersecurity-RedTeam-Roadmap/
├── README.md                          # This file — intro, safety, learning algorithm
├── 01-roadmap/
│   ├── overview.md                    # Full phases 0-6 design (Commit 1)
│   └── how-to-learn.md                # Practical design notes + learning algorithm (Commit 1)
├── 02-fundamentals/
│   ├── computer-basics.md             # Coming — OS, files, processes
│   ├── linux-filesystem.md            # Coming — Linux filesystem explained fully
│   └── linux-commands.md              # Coming — daily commands, with lab examples
├── 03-networks/
│   ├── network-basics.md              # Coming — IP, DNS, ports, TCP/UDP, HTTP
│   └── network-tools.md               # Coming — ping, ss, ip, dig, output meaning
├── 04-web/
│   ├── web-basics.md                  # Coming — how websites work
│   └── owasp-simple.md                # Coming — OWASP Top 10 in simple words
├── 05-tools/
│   ├── tools-overview.md              # Coming — tool categories
│   ├── recon-tools.md                 # Coming — recon tools explained
│   ├── web-tools.md                   # Coming — web tools explained
│   └── password-system-tools.md       # Coming — password + system tools, test data only
├── 06-practice/
│   ├── safe-labs.md                   # Coming — legal labs list
│   └── practice-notes.md              # Coming — note template, routine, write-ups
└── 07-resources/
    ├── books.md                       # Coming — books with level and why
    ├── courses.md                     # Coming — courses, channels, docs
    └── checklists-glossary.md         # Coming — final checklists + glossary
```

If a folder is empty today, it means that commit has not come yet. Do not skip ahead to advanced tools before fundamentals are done.

---

## Practical design notes — why the order is this order

1. **Fundamentals before tools.** A tool only asks a system a question. If you do not understand the system, you cannot understand the answer. Example: `ss -tln` shows listening ports. If you do not know what a port is, the output is just numbers. Learn ports first, then the tool makes sense.

2. **Linux before hacking.** Most red-team work happens on Linux, and most targets run Linux or Windows servers. If you cannot move in a filesystem, read permissions, and check processes, you cannot test a system safely or explain a finding.

3. **Networks before web attacks.** A website is a network service. If you do not know DNS, IP, ports, and HTTP, web attacks look like magic. Learn the network first, then web logic becomes clear: request goes in, response comes out, cookies and sessions keep state.

4. **One tool deep beats ten tools shallow.** For each tool in this roadmap, you will learn: What it is, Why it is used, When to use it, What its output means, One safe lab example, and Common mistakes. That is deeper, and more useful in a job, than a list of 100 tool names.

5. **Notes before certificates.** Certificates can help, but employers in red teaming check labs, write-ups, and GitHub proof first. Build proof every week: a script that runs, a report that is saved, a write-up you can explain. Certificates come later, after proof exists.

6. **Reporting is a red-team skill, not extra work.** Finding a problem in a lab is only half the job. Writing it clearly — what it is, why it matters, how to reproduce it in a lab, and how to fix it — is the other half. That is why every practice file in this repo ends with a write-up template.

---

## How to practise safely — lab design

**Option A — Your own Kali VM (recommended to start):**
- Kali Linux in VirtualBox or VMware, on your own laptop.
- A second small VM for a vulnerable lab target, only on your own private lab network, never exposed to the internet.
- Test files and test passwords you create yourself.

**Option B — Legal learning platforms:**
- TryHackMe, HackTheBox Academy, PortSwigger Web Security Academy.
- These are made for learning. Follow their rules, use only the lab machines they give you.

**Option C — Your own code and reports:**
- Small Python tools on your own machine, like a password checker with test passwords only, and a system audit that checks only your own machine and localhost ports.
- Save the report, read it, and explain it. That is already red-team practice: check, understand, report.

**Never practise on:** Real websites, real company networks, public Wi-Fi networks, other people's computers, or any system without written permission. If a lab platform says "do not attack outside the lab", follow that rule exactly.

---

## Progress checklist — start here

- [ ] I read the safety rule and I understand it.
- [ ] My lab is ready: Kali VM works, terminal opens, `python3 --version` works.
- [ ] I read `01-roadmap/overview.md` fully, not just the table above.
- [ ] I read `01-roadmap/how-to-learn.md` and I can explain the learning algorithm in my own words.
- [ ] I have a notes file, and I wrote my first page in my own words.
- [ ] I can explain, in simple English, what a Red Team does and what it does NOT do.

When all boxes are checked, you are ready for Phase 1. If not, stay on Phase 0. That is not slow — that is structured.

---

## A note on honesty

This roadmap does not promise a job, a salary, or a certificate. It promises a clear path, if you follow it honestly: learn, practise in labs, write notes, build proof, and explain your work clearly. That combination is what makes a professional hacker different from someone who only watched hacking videos.

Start with Phase 0. One step at a time. No shortcuts.

---

*Educational use only. All practice must be on systems you own or have written permission to test. This repo does not contain instructions to attack real systems.*
