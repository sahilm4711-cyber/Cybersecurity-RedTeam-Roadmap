# 01 — Roadmap Overview: Full Design, Beginner to Advanced

## Design idea in one sentence

**Learn how systems work first, then learn how to test them safely in a lab, then learn how to report what you found clearly.**

That is the whole design. Every phase below follows that idea. If you skip the "how it works" part, the testing part will not make sense, and the report part will be weak.

---

## How to read this file

For each phase you will find the same structured sections, so you can predict what is coming and check yourself:

1. **Goal** — what you can do after this phase.
2. **Why this phase** — why it comes here, not earlier or later.
3. **What you learn** — topics, in order, with simple meaning.
4. **Tools explained** — what, why, when, and what output means. No tool is just a name here.
5. **Practical design notes** — how professionals actually think about this phase.
6. **Algorithm for this phase** — step-by-step learning loop for this phase.
7. **Practice tasks (lab only)** — safe tasks, with proof you should save.
8. **Common mistakes** — what beginners usually get wrong.
9. **Checklist to move on** — do not move on until you can check these boxes.

Use this file as your map. The detailed files for each topic will come in later big commits, in the same order as the phases below.

---

## Phase 0 — Mindset, Ethics, and Lab Setup

### Goal
You can explain what a Red Team does, what it does NOT do, and you have a working lab and a notes system.

### Why this phase
Red teaming is a job of trust. A company gives you permission to test its systems because it trusts you will not harm them, steal data, or go beyond permission. If you do not understand ethics and scope first, every later skill is dangerous. Lab setup also matters: if your lab is messy, every later experiment will be messy.

### What you learn
- **Red Team vs Blue Team vs Purple Team, in simple words:**
  - Red Team = tests systems like an attacker would, but with permission, to find weaknesses before a real attacker does.
  - Blue Team = defends systems, watches logs, fixes problems, and responds to attacks.
  - Purple Team = red and blue working together, so defence improves from real test findings.
- **Permission and scope:**
  - Scope = exactly which systems, which time, and which methods are allowed, in writing.
  - Out of scope = everything else. Do not test it, even if you are curious.
  - Rule: If it is not in writing, it is not allowed.
- **Lab setup:**
  - Kali Linux in a virtual machine on your own laptop.
  - A notes folder, a projects folder, and a habit of saving reports.
  - Basic terminal use: open terminal, check `pwd`, `ls`, `whoami`.

### Tools explained
- **VirtualBox / VMware:** Software that lets you run a second computer (a virtual machine) inside your real computer. Why: you can break the lab VM safely, without breaking your real system. When: always for red-team practice. Output meaning: a VM window is a separate computer, with its own files and network settings.
- **Kali Linux:** A Linux system made for security learning, with many security tools already installed. Why: it saves setup time and matches what professionals use in labs. When: for lab practice only, not as your daily system for personal data unless you understand it well.
- **Terminal:** A text window where you type commands. Why: most red-team work is done by commands, not by clicking. When: every day. Output meaning: the terminal prints exactly what the command did, or the error it found — read it fully, do not ignore errors.

### Practical design notes
- Design your lab like a professional designs a workspace: one folder for projects, one folder for notes, one folder for reports. If you cannot find last week's report in 30 seconds, your lab design needs fixing.
- Keep test data separate from real data. Create a `lab-test` folder with sample files you made yourself. Never practise with real passwords or real personal files.
- Write down your lab design on one page: what VMs you have, what network they use (private lab network only), and what you are allowed to test. That one page is your scope note.

### Algorithm for this phase
```
1. Read safety rule in README.md.
2. Explain Red Team / Blue Team in your own words, out loud.
3. Set up Kali VM and test terminal commands: pwd, ls, whoami.
4. Create folders: projects, notes, reports.
5. Write a one-page scope note: what you own, what you will test, what you will NOT test.
6. Record a 2-minute English explanation: "What is a Red Team?"
7. Review next day: can you repeat steps 2 and 3 without notes?
```

### Practice tasks (lab only)
- Open terminal, run `pwd`, `ls -l`, `whoami`, and explain each output in your notes.
- Create a test folder and a test text file with sample text you wrote yourself. Read it back with `cat`.
- Write your scope note and save it as `notes/scope.md` in your own lab folder.

### Common mistakes
- Starting with hacking tools before understanding permission. Fix: Phase 0 first, always.
- Using a real system "just to test quickly". Fix: there is no quick test on a real system without permission.
- Saving notes in random places. Fix: one notes folder, one structure, every time.

### Checklist to move on
- [ ] I can explain Red Team, Blue Team, and scope in simple English.
- [ ] My Kali VM opens, terminal works, and I know my working folder.
- [ ] I have a scope note, and I can say what I will NOT test.
- [ ] I have a notes system I actually use.

---

## Phase 1 — Computer and Linux Fundamentals

### Goal
You can move in Linux confidently: files, folders, permissions, processes, and the full filesystem, and you can explain what each part does.

### Why this phase
Most servers, most lab targets, and most red-team tools run on Linux. If you cannot read a permission number, find a file, or check a running process, you cannot understand a system report, and you cannot test a system safely. This phase is the foundation for everything after it.

### What you learn
- **How a computer works, simply:** CPU runs instructions, memory holds running data, disk holds saved files, operating system manages all three.
- **Linux filesystem, fully (detailed file coming in Commit 4):**
  - `/` = root, the top of everything.
  - `/home` = users' personal folders.
  - `/etc` = system settings files.
  - `/var` = logs and changing data, like `/var/log`.
  - `/tmp` = temporary files.
  - `/usr`, `/bin`, `/sbin` = programs and system tools.
  - Why each folder exists, what happens if you change it, and what you should never change without understanding.
- **Permissions, in depth:**
  - Numbers like `775`, `644`, `600`: what each digit means (owner, group, others), and what read, write, execute mean for files vs folders.
  - Example: `775` for a folder = owner full, group full, others read and open but not change. `600` for a private file = owner read/write only.
  - How to check with `ls -l`, and how to change safely in your own lab with `chmod`.
- **Processes and services:**
  - What a process is, how to list with `ps`, how to check listening ports with `ss -tln` on your own machine only.
  - What a service is, why a database listens on a port, and why localhost-only is safer than exposed to all networks.
- **Daily commands, with meaning, not just names:**
  - `cd`, `ls`, `pwd`, `cat`, `mkdir`, `rm`, `cp`, `mv`, `grep`, `find`, `chmod`, `whoami`, `help` — each with: what it does, when to use it, one lab example, and one common mistake.

### Tools explained
- **Terminal + core commands:** Your main workspace. Why: every later tool is run from here. When: every day. Output meaning: always read the full output, not just the last line.
- **`ls -l`:** Lists files with permissions, owner, size, date. Why: permissions are the first security check. When: before you change any file. Output meaning: first column is permission string, like `-rw-r--r--`.
- **`ss -tln`:** Shows listening TCP ports on your own machine only. Why: a listening port means a service is waiting for connections. When: in a system audit, on your own machine only. Output meaning: `127.0.0.1:5432` = localhost only, safer. `0.0.0.0:5432` = all networks, check this carefully.

### Practical design notes
- Think of the filesystem as a building: `/` is the entrance, `/home` is rooms for users, `/etc` is the control room, `/var/log` is the security camera log. You would not change the control room without knowing what each switch does. Same rule for Linux.
- Permissions are a design, not just numbers. A secure design asks: Who needs to read this? Who needs to change this? Who should NOT even see this? Every permission check in a later audit will ask those three questions.
- Learn one command deeply per day, not ten commands shallowly. For each command, write: purpose, example, output meaning, mistake to avoid.

### Algorithm for this phase
```
For each Linux topic:
1. Read the concept in simple words.
2. Run the command in your own lab with test files you made.
3. Change one thing (for example, chmod 600 vs 644) and observe the output change.
4. Write notes: command, purpose, example, output meaning, mistake.
5. Explain it in English in 2 minutes.
6. Next day, try the command again without notes.
```

### Practice tasks (lab only)
- Build a test folder tree in your lab, with sample files, and practise `cd`, `ls -l`, `cat`, `grep`, `find`.
- Check permissions for your own project folder and one test file, and explain `775` vs `600` in notes.
- Run a system audit on your own machine only: OS, user, folder permissions, localhost ports. Save the report as `audit_report.txt` in your lab folder. Do not publish private details without thinking.
- Explain the Linux filesystem in a one-page diagram in your own words: draw `/`, `/home`, `/etc`, `/var`, `/tmp` and what lives in each.

### Common mistakes
- Running commands as root for everything, without understanding why. Fix: use normal user first, use root only when you understand the command.
- Deleting files with `rm -rf` without checking `pwd` first. Fix: always run `pwd` and `ls` before a delete, and never delete a folder you did not just list.
- Memorising permission numbers without understanding read/write/execute. Fix: break the number into owner/group/others every time, until it is automatic.

### Checklist to move on
- [ ] I can explain the Linux filesystem and permissions in simple English.
- [ ] I can find files, read logs in my own lab, and check processes and localhost ports.
- [ ] I have a system audit report from my own machine that I can explain line by line.
- [ ] I can use 15+ daily commands without copying from notes every time.

---

## Phase 2 — Networking Fundamentals

### Goal
You can explain how computers talk to each other, what an IP, a port, DNS, and HTTP mean, and you can check your own machine's network safely.

### Why this phase
A red teamer tests networked systems. If you do not know how a request travels from your computer to a server and back, you cannot understand a web attack, a port scan result, or a firewall rule. Networking is the map. Without the map, tools are just noise.

### What you learn
- **IP addresses:** How computers are identified on a network. Private vs public, localhost `127.0.0.1` meaning "this computer only".
- **Ports:** Like doors on a computer. Port 80 = web, 443 = secure web, 22 = SSH, 5432 = PostgreSQL database, and so on. A listening port means a service is waiting at that door.
- **TCP vs UDP, simply:** TCP = reliable, checked delivery, like a phone call. UDP = fast, unchecked delivery, like a postcard. Most web traffic uses TCP.
- **DNS:** How a name like `example.com` becomes an IP address. Like a phone book for the internet.
- **HTTP and HTTPS:** How browsers ask for a page (request) and servers answer (response). Status codes: 200 = OK, 404 = not found, 403 = forbidden, 500 = server error.
- **Packets, simply:** Data broken into small pieces, sent, and put back together. Tools like Wireshark in a lab let you see packets, but you start with concepts, not packet deep-dives.

### Tools explained
- **`ping`:** Sends a small test message to check if another computer in your lab responds. Why: basic connectivity check. When: in your own lab only. Output meaning: replies + time = reachable, no reply = check network, firewall, or wrong address.
- **`ss -tln` and `ip a`:** `ss` shows listening ports on your own machine. `ip a` shows your own network interfaces and IPs. Why: audit your own machine first, before any other network work. When: system audit, lab setup.
- **`dig`:** Asks DNS questions, like "What IP is this name?" Why: understand DNS, not just memorise it. When: in lab, for lab names and public learning domains, not for scanning others.
- **Wireshark (lab only):** Shows packets on your own lab network. Why: see HTTP requests and responses in real life. When: after you understand HTTP basics, in your own lab only, never on a network you do not own.

### Practical design notes
- Design thinking: Every network service is a design choice. A service listening on `127.0.0.1` is designed for local use only. A service listening on `0.0.0.0` is designed to accept connections from any network. That one difference changes the risk completely. Always ask: "Who is this service designed for? Local only, or everyone?"
- A port scan result is not a vulnerability by itself. An open port means a service is there. The next questions are: What service is it? What version? Is it supposed to be open? Is it patched? Is it exposed to the wrong network? Those questions are the red-team thinking, not just the scan output.
- Learn networks by drawing: draw your lab computer, your lab server, the router between them, and label IPs and ports. If you can draw it, you understand it.

### Algorithm for this phase
```
For each networking topic:
1. Learn the concept in simple words (IP, port, DNS, HTTP).
2. Draw it: computer -> network -> server, with labels.
3. Check it on your own machine: ip a, ss -tln, ping localhost.
4. Write notes: concept, example, output meaning, why it matters for security.
5. Explain it in English: "What happens when I open a website in my lab?"
```

### Practice tasks (lab only)
- Draw your lab network diagram with IPs and ports, in notes.
- Check your own machine: `ip a`, `ss -tln`, `ping 127.0.0.1`, and explain each output.
- In your own lab, start a simple local web server if your course/lab guides you, and check which port it listens on, and whether it is localhost-only or not. Stop the server after practice.
- Write a one-page note: "What happens, step by step, when a browser asks for a page in my lab?"

### Common mistakes
- Thinking an open port automatically means "hacked" or "vulnerable". Fix: open port = service present. Check service, version, exposure, and patch level next.
- Scanning networks you do not own "just to learn". Fix: lab only, always. Scanning without permission is not learning, it is unauthorised testing.
- Memorising port numbers without understanding services. Fix: for each port, learn: service name, what it does, and why it would listen.

### Checklist to move on
- [ ] I can explain IP, port, DNS, TCP/UDP, and HTTP in simple English.
- [ ] I can draw a lab network and label IPs and ports.
- [ ] I can audit my own machine's listening ports and explain localhost-only vs exposed.
- [ ] I can explain a full web request journey, from browser to server and back.

---

## Phase 3 — Web Fundamentals and OWASP

### Goal
You can explain how a website works, how requests, cookies, and sessions work, and what the OWASP Top 10 problems mean in simple words, with lab examples only.

### Why this phase
Most red-team work today involves web applications. Companies live on the web. If you do not understand requests, responses, cookies, sessions, and databases behind a website, web security will look like magic tricks. Learn the web first, then security problems become logical: "Where does user input go? Where is it trusted without checking?"

### What you learn
- **How a website works:** Browser sends request, server processes it, database may be involved, server sends response, browser shows page. Cookies store small data, sessions keep you logged in.
- **Requests and responses:** Method (GET, POST), URL, headers, body, status codes. What each part means, and how to read them in browser developer tools in your own lab.
- **Authentication and sessions, simply:** Login checks who you are. Session keeps you logged in without asking for password on every page. If session handling is weak, security problems follow.
- **OWASP Top 10, in simple words (detailed file coming):**
  - Broken Access Control: a user can reach something they should not reach.
  - Cryptographic Failures: sensitive data not protected properly.
  - Injection: user input is trusted and run as code or database query, instead of being treated as plain data.
  - Insecure Design: the design itself misses security checks from the start.
  - Security Misconfiguration: default settings, open debug pages, or extra features left on.
  - Vulnerable Components: old libraries or software with known problems, not updated.
  - Identification and Authentication Failures: weak login, weak session, or weak password handling.
  - Software and Data Integrity Failures: updates or data are trusted without checking they are genuine.
  - Security Logging and Monitoring Failures: attacks are not logged or not noticed in time.
  - Server-Side Request Forgery (SSRF): server is tricked into making requests it should not make.
  - For each: simple meaning, lab example concept, how a professional would report it, and how a developer would fix the design — not a real exploit against a real site.

### Tools explained
- **Browser developer tools:** Built into your browser. Why: see requests, responses, cookies, and page code for a site you are allowed to test in a lab. When: learning web basics, in your own lab site only.
- **curl:** A terminal tool to send a request and see the response. Why: understand requests without a browser hiding details. When: lab learning, for your own lab server. Output meaning: headers + body = what the server actually sent back.
- **Burp Suite (lab only, Community Edition for learning):** A tool that sits between browser and lab server, so you can see and change requests in a lab. Why: learn how requests work, and how changing input changes response, in a lab you own. When: only for lab sites you own or legal lab platforms. Never for real sites without written permission.

### Practical design notes
- Web security design question, always: "Where does user input enter? Where is it checked? Where is it used?" If input is used without checking, that is a design weakness. Follow input through the system like following water through pipes. Where it leaks, there is a problem.
- A professional web test in a lab follows this algorithm: Map the site (pages, forms, parameters) -> Understand normal behaviour -> Test one input change at a time in lab -> Note response change -> Write finding with reproduction steps in lab and fix suggestion. Never test many random payloads on a real site.
- Reporting matters more than finding: a finding without clear reproduction in lab, impact in simple words, and fix suggestion is not a professional report yet.

### Algorithm for this phase
```
For each web topic:
1. Learn normal behaviour first: how does this feature work when used correctly?
2. Map it: pages, forms, inputs, cookies, sessions.
3. In your own lab site, change one input at a time and note the response change.
4. Ask: "Was this input checked? Where was it used? What changed?"
5. Write a lab write-up: what you tested, what you observed, what the design fix would be.
6. Explain it in English in 3 minutes.
```

### Practice tasks (lab only)
- Use browser developer tools on your own lab page, and note request, response, cookies, and status code.
- Use PortSwigger Academy labs (legal, made for learning) for OWASP concepts, one topic at a time, and write notes in your own words.
- Build a small test page in your lab (if your course guides you) with a form, and practise following input from form to response, in your own code.
- Write one lab write-up per OWASP topic you study: simple meaning, lab observation, fix idea.

### Common mistakes
- Jumping to payload lists before understanding normal web behaviour. Fix: normal first, test second.
- Testing real websites "just to see". Fix: lab only, always. No exceptions for curiosity.
- Copying a write-up without understanding it. Fix: write in your own words, with your own lab observation.

### Checklist to move on
- [ ] I can explain request, response, cookie, session, and database role in a website.
- [ ] I can explain each OWASP Top 10 item in simple words, with a lab concept.
- [ ] I have 3+ lab write-ups I can explain without reading them word for word.
- [ ] I can use developer tools and curl in a lab and explain the output.

---

## Phase 4 — Systems and Active Directory Basics

### Goal
You can explain how Windows and Linux systems work, how users, groups, and services work, and what Active Directory is, in simple words, with lab practice only.

### Why this phase
Real companies do not run one computer. They run many computers managed together. Active Directory (AD) is how many Windows networks manage users, computers, and permissions together. A red teamer who does not understand AD cannot understand a company network test. You start with basics, not advanced AD attacks.

### What you learn
- **Windows basics for red teamers:** Users, groups, services, registry concept, PowerShell basics, event logs concept.
- **Linux systems recap, deeper:** Users, groups, sudo concept, services, cron jobs concept, logs in `/var/log`.
- **Active Directory, simply:**
  - Domain = a group of computers managed together.
  - Domain Controller = the main computer that checks logins and permissions for the domain.
  - Users, groups, Organizational Units (OUs), Group Policy = rules that apply to users/computers.
  - Kerberos and NTLM, simply: ways computers prove who you are, without sending your password in plain text every time.
  - Trusts: when one domain trusts another domain's users.
- **Why AD matters for security:** If one account has too much permission, or one computer is weak, an attacker in a lab can move from one computer to another. That is called lateral movement in lab terms. You learn the concept and lab defence first, not real network attacks.

### Tools explained
- **PowerShell (own lab Windows VM only):** Windows command tool. Why: manage and check Windows systems in a lab. When: in your own Windows lab VM. Output meaning: like Linux terminal, but for Windows.
- **BloodHound (lab only):** A tool that draws AD relationships as a graph: who can reach what, who has what permission. Why: AD is hard to understand as a list, easier as a map. When: only in your own AD lab, built by you for learning, never on a real company network without written permission and scope.
- **System audit tools (your own machine):** Your own Python system audit, plus `ps`, `ss`, and log checks. Why: check your own system first, before any other system.

### Practical design notes
- AD design question: "Who has permission to what, and why?" Most AD security problems are permission design problems: too many admins, old accounts still active, service accounts with too much power, or weak password policy. A professional test checks design, not just tools.
- Build your AD lab slowly: one domain controller VM, one or two client VMs, all on a private lab network, all owned by you. Do not connect a lab AD to a real network.
- Logs are your friend: Blue Team watches logs. Red Team in a lab should also learn what logs show, because a good test is one that can be detected and explained, not one that hides forever.

### Algorithm for this phase
```
1. Learn Windows/Linux system basics in simple words.
2. Build a small private lab: 1 domain controller + 1 client, all yours.
3. Create test users and groups yourself, with test passwords only.
4. Check permissions: who can read what, who can change what.
5. Draw the AD map in notes: users, groups, computers, permissions.
6. Write a lab report: what you built, what permissions you found, what design fix you would suggest.
```

### Practice tasks (lab only)
- Set up a small Windows lab VM (if your course/lab guides you) and practise user/group creation with test accounts only.
- Audit your own Linux machine again, but deeper: users, groups, services, and logs.
- Draw an AD diagram in notes for a small test lab: domain, controller, users, groups.
- Write a report for your lab: permissions found, risks in lab terms, and fix ideas.

### Common mistakes
- Trying advanced AD attacks before understanding users, groups, and permissions. Fix: basics first, always.
- Building an AD lab on a real network or with real accounts. Fix: private lab network, test accounts only, always.
- Collecting tool output without understanding it. Fix: for each output line, write what it means in your notes.

### Checklist to move on
- [ ] I can explain domain, domain controller, user, group, OU, and Group Policy simply.
- [ ] I have a small private lab diagram I can explain.
- [ ] I can check permissions on Windows and Linux in a lab and explain them.
- [ ] I have a lab report with findings and fix ideas, in my own words.

---

## Phase 5 — Red Team Tools and Techniques (Lab Only)

### Goal
You can explain red-team tool categories, use each tool in a lab you own, understand its output, and write a clear lab finding with a fix idea.

### Why this phase
Now, and only now, tools make sense. You know systems, networks, web, and AD basics. Tools are now helpers to test those systems in a lab, not magic. This phase is about using tools correctly, safely, and with clear reporting — not about collecting the most tools.

### What you learn, in order
- **Recon in lab:** Finding information about a lab target you own: what services are running, what pages exist, what versions are used. Tools: Nmap, dig, whois, ffuf (lab only).
- **Initial access in lab:** How a lab test starts: a weak lab login, a lab web problem, or a lab service problem — all in your own lab, with permission and scope written down first.
- **Privilege escalation in lab:** How a low-permission lab account might reach higher permission in a lab, due to a lab misconfiguration, and how to fix that design. Lab only, with test accounts.
- **Persistence and defence evasion concepts:** What these terms mean, why defenders watch for them, and why a professional test must be detectable and reported, not hidden.
- **Lateral movement concept in lab:** How permission design in a lab network could allow movement from one lab computer to another, and how to fix permission design.
- **Reporting a lab finding:** Title, simple meaning, impact, reproduction steps in lab, evidence (lab output, with private details removed), fix suggestion.

### Tools explained (detailed files coming)
- **Nmap:** Network scanner for your own lab network. What: checks which ports are open and what services answer. Why: map a lab network before testing. When: lab only, with scope in writing. Output meaning: open port = service present, not automatically vulnerable. Check service and version next.
- **ffuf / Gobuster:** Web directory and parameter discovery for lab sites you own. What: tries common page names to find pages that exist in a lab. Why: map a lab website. When: lab only. Output meaning: found page = page exists, check if it should be public or not.
- **Metasploit (lab only):** A framework with lab exploit modules for vulnerable lab machines made for learning. What: tests a known lab vulnerability in a controlled way. Why: learn how a vulnerability works in a lab, and how to fix it. When: only on vulnerable lab VMs made for learning, never on real systems.
- **Burp Suite (lab only):** Web testing tool, as in Phase 3, now used deeper in lab web tests.
- **BloodHound (lab only):** AD graph tool, as in Phase 4, now used to understand lab AD permissions.

### Practical design notes
- Every tool use in a professional test starts with a written scope and ends with a written report. No scope, no test. No report, no professional value.
- A tool is a question, not an answer. Nmap asks: "What services are listening in this lab?" The answer needs human thinking: "Should this service be listening? To whom? Is it patched? Is it needed?"
- In a lab, always test one change at a time. If you change five things and something breaks, you will not know which change caused it. One change, one observation, one note. That is the algorithm for all red-team lab work.
- Defence matters: for every attack concept you learn in a lab, also learn the defence: how would a Blue Team detect this in logs, and how would a developer fix the design? A red teamer who only knows attack, without defence, is only half a professional.

### Algorithm for this phase
```
For each tool and technique in lab:
1. Write scope: what lab target, what time, what methods allowed.
2. Learn normal behaviour of the lab target first.
3. Run the tool in lab, with one test at a time.
4. Read full output, line by line, and write what each line means.
5. Ask: "Is this expected? Should this be open? What is the fix?"
6. Write a lab finding: title, meaning, impact, lab reproduction, fix.
7. Explain the finding in English in 3 minutes, without reading notes word for word.
```

### Practice tasks (lab only)
- Map a lab network you own with Nmap, and write a map with services and versions, in notes.
- Map a lab website you own with a directory tool, and note which pages should be public vs private in lab design.
- Do one complete lab engagement, start to finish, on a legal lab platform or your own vulnerable VM: scope note -> recon -> test -> finding -> report -> fix idea.
- Save all lab reports in your portfolio folder, with private details removed.

### Common mistakes
- Running tools on networks or sites without permission, "just to practise". Fix: lab only, always, no exceptions.
- Saving tool output as a report, without explanation. Fix: output is evidence, not a report. Add meaning, impact, and fix.
- Learning 20 tools shallowly, instead of 5 tools deeply. Fix: deep first, then expand slowly.

### Checklist to move on
- [ ] I can explain each major tool category and one tool in each category deeply.
- [ ] I have 3+ complete lab engagements with scope, findings, and fix ideas.
- [ ] I can explain a lab finding in English clearly, with impact and fix.
- [ ] I have a portfolio folder with reports I am proud to show (private details removed).

---

## Phase 6 — Red Team Operations, Reporting, and Job Preparation

### Goal
You can plan a small lab engagement, run it safely, write a professional report, build a portfolio, and prepare for interviews honestly.

### Why this phase
Skills without proof do not get jobs. A portfolio with clear, honest lab work, plus clear communication, is what interviewers check. This phase turns your learning into proof that other people can read and trust.

### What you learn
- **Planning a lab engagement:**
  - Scope in writing, time plan, methods allowed, rules for stopping if something goes wrong.
  - Communication plan: who to tell, when, and how, in a real job context.
  - Safety plan: what to do if you find a serious problem in a lab, and how to report it immediately.
- **Reporting, fully:**
  - Executive summary in simple words: what was tested, what was found, what matters most.
  - Technical findings: one finding at a time, with title, severity in lab terms, simple meaning, impact, lab reproduction steps, evidence, and fix suggestion.
  - Appendix: scope, tools used in lab, and timeline.
  - Language: clear, honest, no exaggeration. If a finding is low impact in lab, say low. Honesty builds trust.
- **Portfolio building:**
  - GitHub repos with real projects, not copied notes: password lab, system audit, roadmap, lab write-ups.
  - Write-ups: each with scope, method, finding, fix, and what you learned.
  - Resume: skills with proof links, not just skill names. Example: "System Audit (Python, own machine only) — GitHub link, report sample" is stronger than "Hacking" alone.
- **Interview preparation:**
  - Explain a lab project in 3 minutes, without notes.
  - Answer: "What did you test? What did you find? How did you fix the design? What would you do differently?"
  - Be honest about what you do not know yet, and what you are learning next. Interviewers respect honesty more than pretending.
- **Certifications, honestly:**
  - After proof exists, consider: eJPT / PNPT for practical beginner red team, then OSCP for deeper practical work, CRTP for AD focus later.
  - Certificates are proof of a test passed, not a replacement for labs. Do not collect certificates without labs.

### Tools explained
- **Report template:** A markdown or document template with sections: Summary, Scope, Findings, Fixes, Appendix. Why: consistency makes reports easy to read and trust. When: every lab engagement.
- **GitHub portfolio:** Your public proof. Why: employers can see your work, your commit history, and your writing. When: after each big project, with private details removed.
- **Notes and write-up system:** Your personal knowledge base. Why: in a job, you will reuse notes, checklists, and templates. When: every day, not just before interviews.

### Practical design notes
- Design your portfolio like a shop window: a visitor should understand in 30 seconds what you can do, and in 3 minutes find proof. Put your best 3 projects first, with clear README files, not 20 unfinished projects.
- A good report is designed for two readers: a manager who needs the summary in simple words, and a technical person who needs reproduction steps and fix details. Write both sections, clearly separated.
- Job preparation is also a design: list the jobs you want, read their skill lists, map each skill to a proof item in your portfolio, and fill gaps one by one. That map is more useful than applying to 100 jobs with the same resume.

### Algorithm for this phase
```
1. Choose one lab engagement to polish as portfolio proof.
2. Write scope, method, findings, fixes in the report template.
3. Remove private details, check the report for honesty and clarity.
4. Add it to GitHub with a clear README: what, why, how to run in lab, safety note.
5. Practise explaining it in 3 minutes, out loud, in English.
6. Map it to a job skill list: which skill does this prove?
7. Repeat for your next best project, until you have 3 polished proofs.
```

### Practice tasks (lab only)
- Polish one lab report to portfolio quality, using the template above.
- Build a one-page portfolio README: who you are, what you can do, 3 best proofs with links.
- Practise a mock interview: explain one project in 3 minutes, answer follow-up questions honestly.
- Make a job-skill map: 10 jobs you like, skills they ask for, and which portfolio item proves each skill, and what gap remains.

### Common mistakes
- Publishing reports with private details, real hostnames, or real credentials. Fix: remove private details before publishing, always.
- Exaggerating findings to look impressive. Fix: honest severity, honest scope, honest learning. Trust is the job.
- Waiting until you feel "ready" to build a portfolio. Fix: start with what you have now, polish one item at a time. Ready comes from proof, not from waiting.

### Checklist to finish the roadmap
- [ ] I have 3 polished lab reports with scope, findings, and fixes.
- [ ] My GitHub portfolio shows my work clearly, with safety notes.
- [ ] I can explain my best project in 3 minutes, in simple English.
- [ ] I have a job-skill map and a clear next learning step.
- [ ] I understand that learning does not stop here — new tools, new web problems, and new AD designs come every year, and the learning algorithm in this repo still applies.

---

## What comes next after this file?

Later big commits will turn each phase above into detailed files:

- `02-fundamentals/` — computer basics, Linux filesystem fully explained, Linux commands with lab examples.
- `03-networks/` — network basics, network tools with output meaning.
- `04-web/` — web basics, OWASP in simple words.
- `05-tools/` — tools overview, recon, web, password and system tools, each explained deeply, lab only.
- `06-practice/` — safe labs list, practice notes template, routine.
- `07-resources/` — books, courses, checklists, glossary.

Do not skip phases. Follow the map, one big commit at a time, one phase at a time, with the learning algorithm from `how-to-learn.md`.

---

*Educational use only. All practice must be on systems you own or have written permission to test.*
