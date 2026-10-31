# Final Checklists — Visual Notes

Lab only · own machine · test data only

Not tick-boxes to collect. Each row is: can you do it, from memory, in lab, and what proof shows it? If the proof column is empty, that row isn't done yet.

## Phase 1 — Fundamentals

| Can you do it? | Proof, in lab |
|---|---|
| Explain CPU, RAM, disk, OS, kernel, shell in plain English | 2-minute explanation, without notes |
| Read a path and say what each part means | `/home/your-user/lab-test/test.txt`, explained part by part |
| Read `ls -l` and explain owner, group, permissions | One test file, read back in plain words |
| Explain program vs process vs service | One example each, from your own machine |
| Run `ps` and `ss` and explain every line | `ps aux \| head -20`, `ss -tln`, localhost vs exposed, in your own words |

## Phase 2 — Linux

| Can you do it? | Proof, in lab |
|---|---|
| Navigate, read, create, copy, move, delete in `~/lab-test` | Lab folder, test files only, no real files touched |
| Find a file by name and by content, in lab | `find` + `grep`, on lab files only |
| Read and change permissions: `rwx`, octal, `chmod`, `chown`, `umask` | Test file: 644 → 600, re-checked, explained |
| Combine commands with pipes and redirection | One pipeline you built yourself, output explained |
| Explain the filesystem tree: `/`, `/home`, `/etc`, `/var/log` | Draw the tree, from memory, with one line per folder |

## Phase 3 — Networks

| Can you do it? | Proof, in lab |
|---|---|
| Explain IP, ports, TCP/UDP, DNS, in one diagram | Diagram drawn from memory, no notes |
| Check what is listening, and whether it's localhost or exposed | `ss -tln` on your own machine, each port explained |
| Use `ping`, `ip`, `dig`, `curl` in lab, and explain output | One run each, output in your own words |
| Explain what a packet, a socket, and a subnet are | One line each, then a lab example |
| Say what you will NOT test, and why | Scope rule, in your own words: own lab only, written permission only |

## Phase 4 — Web

| Can you do it? | Proof, in lab |
|---|---|
| Explain URL, HTTP request/response, status codes, headers, cookies, sessions | One localhost lab request, traced end to end |
| Run a localhost lab server and check it | `python3 -m http.server 8000 --bind 127.0.0.1`, checked with `curl` |
| Explain OWASP A01–A10, one line each | Table, from memory, with a lab example per risk |
| Explain a web finding like a professional: finding, impact, evidence, fix | One lab write-up, scrubbed, placeholders only |
| Say what a lab app is, and why real sites are off-limits | Scope rule, in your own words |

## Phase 5 — Tools

| Can you do it? | Proof, in lab |
|---|---|
| Start from a question, not a tool name | One lab task: question → tool → output meaning |
| Use recon tools in lab / in-scope rooms only | DNS, browser dev tools, `curl` — lab targets only |
| Use web tools in lab only: dev tools, proxy, scanner | Own lab app, test accounts only, output explained |
| Explain a tool's output, and where it stops proving anything | One tool run, with limits written down |
| Clean up after a lab session | Services stopped, notes scrubbed, lab closed — one line proof |

## Phase 6 — Password, System, Safe Labs

| Can you do it? | Proof, in lab |
|---|---|
| Check a test password: length first, then types, then verdict | Test words only, never a real password |
| Explain hash, salt, and slow password hashing | One line each, then the store-and-check flow, in your own words |
| Audit your own machine: OS, permissions, ports, updates | Safe audit summary, placeholders only, private notes |
| Build a safe lab: purpose, separation, test data, network, cleanup | One lab, built with the BUILD_A_SAFE_LAB steps, in your own notes |
| Clean up a lab, every time | `ss -tln` re-checked, notes scrubbed, services stopped |

## Phase 7 — Practice + Resources

| Can you do it? | Proof, in lab |
|---|---|
| Write a 5-line practice note after a session | Question, did, result, stuck, next — placeholders only |
| Write a failure note, and use it next time | Tried, happened, think, next — one real lab failure, written down |
| Review last week's task from memory, with a small change | Re-run, in lab, with a new 5-line note |
| Pick 1 book + 1 course/room at a time, and finish it | Current book + course, in your private map, with a lab write-up |
| Explain one topic out loud, in simple English, without notes | 5-minute private recording, one improvement noted |

## Final proof — the portfolio, in one table

| Proof item | Where it lives | Scrubbed? |
|---|---|---|
| Lab scripts that run (password checker, system audit) | Your own repo / lab folder | Test data only, no real details |
| Lab write-ups: finding, impact, evidence, fix | Your own repo / notes | Placeholders only, no real username / IP / token |
| Visual notes you can explain | This roadmap repo, your own words | — |
| Practice notes, weekly summaries | Private notes | Placeholders only |

**Done = can do it, from memory, in lab, with proof you can explain.** Not done = read it once, watched a video, or collected a certificate with no lab behind it.

Next → `what-next.md`

---
*Lab only: systems you own or have written permission to test, test data only.*
