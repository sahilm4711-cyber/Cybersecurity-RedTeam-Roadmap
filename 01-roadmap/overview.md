# 01 — Roadmap Overview: Beginner to Advanced

The whole design fits in one line: learn how systems work first, then learn how to test them safely in a lab, then learn how to report what you found clearly.

Skip the first part and the rest falls apart. You can't test what you don't understand, and you can't write a convincing report about a test you didn't understand either. Every phase below follows that order, and the detailed files in later commits follow the same order within each phase.

Use this file as your map. It tells you what each phase is for, what you'll actually learn, which tools you'll meet and how to read their output, and what a practitioner keeps in mind while working through it.

---

## Phase 0 — Mindset, Ethics, and Lab Setup

You walk away able to say what a red team does, what it doesn't do, and what you are and aren't allowed to touch — and you have a lab that actually works, plus a notes system you use instead of just planning to use.

This comes first because red teaming is a job of trust. A company hands you permission to test its systems because it trusts you won't harm them, steal data, or wander beyond scope. If you don't grasp ethics and scope before you learn tools, every later skill is dangerous. Lab setup matters for a duller reason: a messy lab makes every later experiment messy.

Red team, blue team, purple team, in plain terms: red tests systems the way an attacker would, but with permission, to find weaknesses before a real attacker does. Blue defends — watches logs, fixes problems, responds. Purple is red and blue working together, so defence improves from real findings.

Scope is the written agreement: exactly which systems, which times, and which methods are allowed. Everything else is out of scope, even if you're curious. The rule is blunt: if it isn't in writing, it isn't allowed.

Your lab is Kali Linux in a virtual machine on your own laptop, with a notes folder, a projects folder, and a habit of saving reports. VirtualBox or VMware lets you run a second computer inside your real one, so you can break the lab VM without breaking your real system. Kali is a Linux system built for security learning, with many tools already installed — it saves setup time and matches what practitioners use in labs, but it's a lab system, not a place to keep personal data unless you really know what you're doing. The terminal is where most of this work happens. It prints exactly what a command did, or the error it hit. Read the whole output. Ignoring errors is how beginners waste hours.

Set your lab up like a workspace, not a junk drawer. One folder for projects, one for notes, one for reports. If you can't find last week's report in thirty seconds, fix the layout before you add more work. Keep test data separate from real data — a folder of sample files you made yourself, test passwords only. And write a one-page scope note: what VMs you have, that they sit on a private lab network only, and what you will and won't test. That page is worth more than it looks; it's the habit that keeps you legal later.

In the lab, open a terminal and run `pwd`, `ls -l`, and `whoami`. `pwd` tells you where you actually are — check it before you ever delete anything. `ls -l` shows permissions, owner, size, and date; the first column is the permission string, and learning to read it is your first security check. `whoami` tells you which user you're running as. Then create a test folder and a test text file with sample text you wrote, read it back with `cat`, and write your scope note down. You're ready to move on when you can explain red team, blue team, and scope in plain English, your VM opens and the terminal works, and you have a scope note and a notes system you actually use.

---

## Phase 1 — Computer and Linux Fundamentals

You walk away able to move around Linux confidently: files, folders, permissions, processes, the whole filesystem, and you can explain what each part does instead of just reciting paths.

This is the foundation because most servers, most lab targets, and most red team tools run on Linux. If you can't read a permission number, find a file, or check a running process, you can't read a system report, and you can't test a system safely.

Start with how a computer works at a useful level of detail: the CPU runs instructions, memory holds running data, disk holds saved files, and the operating system manages all three. Then the filesystem, which is worth learning as a building: `/` is the entrance, the top of everything. `/home` holds users' personal folders. `/etc` is the control room, system settings. `/var` holds logs and changing data, including `/var/log`. `/tmp` is temporary files. `/usr`, `/bin`, and `/sbin` hold programs and system tools. You wouldn't flip switches in a control room without knowing what they do; same rule applies here.

Permissions are a design, not just numbers. A permission like `775` on a folder breaks down as owner, group, others: owner full access, group full access, others can read and open but not change. `600` on a private file means owner read and write only. Every permission check later asks the same three questions: who needs to read this, who needs to change it, who shouldn't even see it? Check with `ls -l` before you change anything, and change safely in your own lab with `chmod`.

Processes and services follow naturally. A process is a running program; list them with `ps`. A service is a program waiting to do something, often listening on a port. `ss -tln` on your own machine shows listening TCP ports, and this is where the output really matters: `127.0.0.1:5432` means localhost only, so only your own machine can reach it — safer by design. `0.0.0.0:5432` means all networks, which deserves a hard look. That's a database port, PostgreSQL, and the difference between those two lines is the difference between a local service and an exposed one.

Daily commands — `cd`, `ls`, `pwd`, `cat`, `mkdir`, `rm`, `cp`, `mv`, `grep`, `find`, `chmod`, `whoami` — are best learned one deeply per day, not ten shallowly. For each one, write what it does, when you'd reach for it, one lab example, and the mistake you made with it. Two habits will save you repeatedly: always run `pwd` and `ls` before a delete, and don't run everything as root just because it's easier. Use a normal user first; reach for root only when you understand why the command needs it.

In the lab, build a test folder tree with sample files and practise moving through it, then check permissions on your own project folder and a test file and explain `775` versus `600` in your notes. Run a system audit on your own machine only — OS, user, folder permissions, localhost ports — and save it as `audit_report.txt`. Draw the filesystem in your own words, one page, `/` through `/tmp` and what lives in each. You're ready to move on when you can explain the filesystem and permissions cold, find files, read your own lab logs, check processes and localhost ports, explain your audit report line by line, and use fifteen or more daily commands without copying from notes every time.

---

## Phase 2 — Networking Fundamentals

You walk away able to explain how computers talk to each other — IP, ports, DNS, HTTP — and to check your own machine's network safely.

A red teamer tests networked systems. If you don't know how a request travels from your computer to a server and back, a port scan result is just noise and a firewall rule is just a line. Networking is the map.

An IP address identifies a computer on a network. Private addresses stay inside a local network; `127.0.0.1` means "this computer only." A port is like a door on a computer: 80 is web, 443 is secure web, 22 is SSH, 5432 is a PostgreSQL database. A listening port means a service is waiting at that door. TCP is reliable, checked delivery, like a phone call; UDP is fast, unchecked delivery, like a postcard. Most web traffic rides TCP. DNS turns a name like `example.com` into an IP address — a phone book for the internet. HTTP is the conversation itself: the browser sends a request, the server sends a response. Status codes tell you the outcome at a glance: 200 means OK, 404 means not found, 403 means forbidden, 500 means the server hit an error. Packets are just data broken into small pieces, sent, and reassembled; you'll see them in Wireshark later, but start with the concepts.

For tools, start with your own machine. `ip a` shows your own interfaces and IPs — audit yourself before anything else. `ss -tln` shows your listening ports. `ping` sends a small test message to check whether another machine in your lab responds; replies plus a time mean it's reachable, no reply means check the network, the firewall, or whether you typed the wrong address — and only ever ping machines in your own lab. `dig` asks DNS questions, like what IP a name resolves to; use it for lab names and public learning domains, not for probing other people's infrastructure. Wireshark comes after you understand HTTP basics, and only on your own lab network — it shows real packets, real requests and responses, which is illuminating and also exactly why it must stay in the lab.

The design question that runs through all of this: who is this service designed for? A service on `127.0.0.1` is designed for local use only. A service on `0.0.0.0` accepts connections from any network. That one difference changes the risk completely. And an open port on its own is not a vulnerability — it's a service being present. The real questions come after: what service, what version, is it supposed to be open, is it patched, is it exposed to the wrong network? Those questions are the red team thinking, not the scan output.

Draw your lab network: your computer, your lab server, the router between them, IPs and ports labelled. If you can draw it, you understand it. In the lab, check `ip a`, `ss -tln`, and `ping 127.0.0.1`, and explain each output. If a course or lab guides you, start a simple local web server, check which port it listens on and whether it's localhost-only, then stop it when you're done. You're ready to move on when you can explain IP, ports, DNS, TCP/UDP, and HTTP in plain English, draw a labelled lab network, audit your own listening ports and explain localhost-only versus exposed, and walk through a full web request from browser to server and back.

---

## Phase 3 — Web Fundamentals and OWASP

You walk away able to explain how a website works — requests, cookies, sessions — and what each OWASP Top 10 problem means in plain words, with lab examples only.

Most red team work today touches web applications. Companies live on the web. If you don't understand requests, responses, cookies, sessions, and the database behind a site, web security looks like magic tricks. Learn the web first, and security problems become logical: where does user input go, and where is it trusted without being checked?

The flow is simple to state: browser sends a request, server processes it, a database may be involved, server sends a response, browser shows the page. Cookies store small data in the browser; sessions keep you logged in without asking for your password on every page. A request has a method (GET, POST), a URL, headers, and a body; the response has a status code, headers, and a body. Read them in browser developer tools on your own lab site, and you'll start seeing what the browser normally hides from you.

Authentication checks who you are; a session keeps that answer alive across pages. When session handling is weak, security problems follow — that's a pattern you'll see again and again.

The OWASP Top 10, in plain words: broken access control means a user can reach something they shouldn't reach. Cryptographic failures mean sensitive data isn't protected properly. Injection means user input is trusted and run as code or as a database query, instead of being treated as plain data. Insecure design means the design itself missed security checks from the start. Security misconfiguration means default settings, open debug pages, or extra features left on. Vulnerable components means old libraries or software with known problems, not updated. Identification and authentication failures mean weak login, weak session, or weak password handling. Software and data integrity failures mean updates or data are trusted without checking they're genuine. Security logging and monitoring failures mean attacks aren't logged or aren't noticed in time. Server-side request forgery means the server is tricked into making requests it shouldn't make. For each one, the professional version of the work is the same: plain meaning, a lab observation, how you'd report it, and how a developer would fix the design — never a real exploit against a real site.

For tools, browser developer tools are built into your browser and show requests, responses, cookies, and page code for a site you're allowed to test in a lab. `curl` sends a request from the terminal and shows the response — headers plus body, exactly what the server sent back, with no browser smoothing it over. Burp Suite Community Edition sits between your browser and your lab server so you can see and change requests in the lab; it only ever touches lab sites you own or legal lab platforms, never real sites without written permission.

The design question to carry everywhere: where does user input enter, where is it checked, where is it used? Follow input through the system like water through pipes. Where it leaks, there's a problem. And learn normal behaviour before you test anything: map the site — pages, forms, parameters — understand what correct use looks like, then change one input at a time in the lab and note what changes in the response. Jumping to payload lists before you understand normal behaviour is the fastest way to learn nothing.

In the lab, use developer tools on your own lab page and note the request, response, cookies, and status code. Work through PortSwigger Academy labs for OWASP concepts, one topic at a time, in your own words. If a course guides you, build a small test page with a form and follow input from the form to the response in your own code. Write one lab write-up per OWASP topic you study: plain meaning, lab observation, fix idea. You're ready to move on when you can explain requests, responses, cookies, sessions, and the database's role; explain each OWASP item in plain words with a lab concept; talk through three or more lab write-ups without reading them word for word; and use developer tools and curl in a lab and explain the output.

---

## Phase 4 — Systems and Active Directory Basics

You walk away able to explain how Windows and Linux systems work — users, groups, services — and what Active Directory is, in plain words, with lab practice only.

Real companies don't run one computer. They run many, managed together, and Active Directory is how many Windows networks manage users, computers, and permissions together. A red teamer who doesn't understand AD can't understand a company network test. You start with basics here, not advanced AD attacks.

On Windows, learn users, groups, services, the registry concept, PowerShell basics, and the event log concept. On Linux, go deeper on what Phase 1 started: users, groups, the sudo concept, services, the cron concept, logs in `/var/log`. Then AD itself: a domain is a group of computers managed together. A domain controller is the main computer that checks logins and permissions for the domain. Users, groups, Organizational Units, and Group Policy are the pieces — Group Policy being rules that apply to users or computers. Kerberos and NTLM are, simply, ways computers prove who you are without sending your password in plain text every time. Trusts are when one domain trusts another domain's users.

Why AD matters for security comes down to a design question: who has permission to what, and why? Most AD security problems are permission design problems — too many admins, old accounts still active, service accounts with too much power, a weak password policy. A professional test checks the design, not just the tool output. Lateral movement, the term for moving from one computer to another in a network, follows from that design: if one account has too much permission or one computer is weak, movement becomes possible in a lab. Learn the concept and the lab defence first.

PowerShell is the Windows command tool, used in your own Windows lab VM only, and it reads like a Linux terminal for Windows. BloodHound draws AD relationships as a graph — who can reach what, who holds what permission — because AD is hard to grasp as a list and much easier as a map. It only ever runs in your own AD lab, built by you for learning, never on a real company network without written permission and scope. Your own Python system audit, plus `ps`, `ss`, and log checks, covers your own machine.

Build the AD lab slowly: one domain controller VM, one or two client VMs, all on a private lab network, all owned by you, never connected to a real network. Create test users and groups yourself, with test passwords only, check who can read what and who can change what, and draw the map in your notes. Logs deserve attention too — blue teams watch logs, and a good test is one that can be detected and explained, not one that hides forever. You're ready to move on when you can explain domain, domain controller, user, group, OU, and Group Policy simply; explain a small private lab diagram; check permissions on Windows and Linux in a lab and explain them; and have a lab report with findings and fix ideas in your own words.

---

## Phase 5 — Red Team Tools and Techniques (Lab Only)

You walk away able to explain red team tool categories, use each tool in a lab you own, understand its output, and write a clear lab finding with a fix idea.

Now, and only now, tools make sense. You know systems, networks, web, and AD basics, so tools are helpers for testing those systems in a lab, not magic. This phase is about using tools correctly and safely, with clear reporting — not collecting the most tools.

Work through the categories in order. Recon in the lab means finding information about a lab target you own: what services are running, what pages exist, what versions are in use. Initial access in the lab means how a test starts — a weak lab login, a lab web problem, a lab service problem — always with permission and scope written down first. Privilege escalation in the lab means how a low-permission lab account might reach higher permission because of a lab misconfiguration, and how to fix that design. Persistence and defence evasion are concepts to understand, along with why defenders watch for them and why a professional test must be detectable and reported, not hidden. Lateral movement in the lab is the permission-design question from Phase 4, applied across machines.

Nmap maps a lab network you own: it checks which ports are open and what services answer. An open port means a service is present, not automatically vulnerable — check service and version next. ffuf and Gobuster do web directory and parameter discovery for lab sites you own, trying common page names to find pages that exist; a found page raises the question of whether it should be public or not. Metasploit is a framework with lab modules for vulnerable lab machines made for learning; it shows how a known lab vulnerability works in a controlled way and how to fix it, and it never touches real systems. Burp and BloodHound return here, used deeper, in the same lab-only way as before.

Every professional tool use starts with a written scope and ends with a written report. No scope, no test. No report, no professional value. A tool is a question, not an answer: Nmap asks what services are listening in this lab, and the human work is deciding whether each service should be listening, to whom, whether it's patched, whether it's needed. In the lab, test one change at a time — change five things and something breaks, and you won't know which change did it. And for every attack concept you learn in a lab, learn the defence too: how would a blue team detect this in logs, and how would a developer fix the design? A red teamer who only knows attack, without defence, is only half a professional.

In the lab, map a network you own with Nmap and write up services and versions; map a website you own with a directory tool and note which pages should be public versus private by design; and run one complete lab engagement start to finish on a legal platform or your own vulnerable VM — scope note, recon, test, finding, report, fix idea — and save the report in your portfolio folder with private details removed. You're ready to move on when you can explain each major tool category and one tool in each deeply; talk through three or more complete lab engagements with scope, findings, and fix ideas; explain a lab finding clearly, with impact and fix; and have a portfolio folder with reports you're proud to show, private details removed.

---

## Phase 6 — Red Team Operations, Reporting, and Job Preparation

You walk away able to plan a small lab engagement, run it safely, write a professional report, build a portfolio, and prepare for interviews honestly.

Skills without proof don't get jobs. A portfolio of clear, honest lab work, plus clear communication, is what interviewers actually check. This phase turns learning into proof other people can read and trust.

Planning a lab engagement means scope in writing, a time plan, methods allowed, and rules for stopping if something goes wrong — plus, in a real job context, a communication plan for who to tell, when, and how, and a safety plan for reporting a serious problem immediately.

Reporting itself has two readers, and a good report serves both: a manager who needs the summary in plain words, and a technical person who needs reproduction steps and fix details, clearly separated. An executive summary says what was tested, what was found, and what matters most. Each technical finding carries a title, severity in lab terms, plain meaning, impact, lab reproduction steps, evidence with private details removed, and a fix suggestion. An appendix holds scope, tools used in the lab, and timeline. The language rule is simple: clear, honest, no exaggeration. If a finding is low impact in the lab, say low. Honesty builds trust, and trust is the job.

For a portfolio, put your best three projects first with clear README files, not twenty unfinished ones — a visitor should understand in thirty seconds what you can do, and find proof in three minutes. GitHub repos with real projects beat copied notes: a password lab, a system audit, lab write-ups, each with scope, method, finding, fix, and what you learned. On a resume, skills land better with proof links than as bare names: a system audit in Python on your own machine, with a link and a report sample, says more than the word "hacking" alone.

Interview prep is mostly rehearsal: explain a lab project in three minutes without notes, and answer what you tested, what you found, how you'd fix the design, and what you'd do differently. Be honest about what you don't know yet and what you're learning next — interviewers respect that more than pretending. Certifications come after proof exists, not before: eJPT or PNPT for practical beginner red team work, then OSCP for deeper practical work, CRTP for AD focus later. A certificate proves a test was passed; it doesn't replace labs, so don't collect certificates without them.

In practice, polish one lab report to portfolio quality, build a one-page portfolio README, rehearse a mock interview on one project, and make a job-skill map: ten jobs you like, the skills they ask for, which portfolio item proves each skill, and what gap remains. That map beats applying to a hundred jobs with the same resume. You're done with the roadmap when you have three polished lab reports with scope, findings, and fixes; a GitHub portfolio that shows your work clearly with safety notes; a best project you can explain in three minutes in plain English; a job-skill map and a clear next learning step; and the understanding that learning doesn't stop here — new tools, new web problems, and new AD designs arrive every year, and the learning method in this repo still applies.

---

## What comes next after this file?

Later big commits turn each phase into detailed files:

- `02-fundamentals/` — computer basics, Linux filesystem fully explained, Linux commands with lab examples.
- `03-networks/` — network basics, network tools with output meaning.
- `04-web/` — web basics, OWASP in plain words.
- `05-tools/` — tools overview, recon, web, password and system tools, each explained deeply, lab only.
- `06-practice/` — safe labs list, practice notes, routine.
- `07-resources/` — books, courses, glossary.

Don't skip phases. Follow the map, one big commit at a time, one phase at a time, with the learning method from `how-to-learn.md`.

---

*Educational use only. All practice must be on systems you own or have written permission to test.*
