# 02 — Fundamentals: Computer Basics, in Simple Words

## What this file is

This is Phase 1, part 1. Before Linux commands, before networks, before tools, you need to understand the machine itself.

If you can explain this file in simple English, and do the lab tasks on your own machine only, you have the foundation for everything after it. No shortcuts here — each section has: simple explanation, practical design notes, algorithm, lab example, and common mistakes.

**Safety rule for this whole file:** All lab tasks are on your own computer only. Do not check, change, or read other people's computers, files, or processes.

---

## 1. A computer, in one picture

Think of a computer as a small office:

- **CPU (Central Processing Unit) = the worker.**
  The worker does the work, one instruction at a time, very fast. When you open a program, the CPU runs its instructions.
  Simple meaning: CPU = brain that follows instructions.

- **Memory (RAM) = the worker's desk.**
  The desk holds papers the worker is using right now. When you close a program, its papers are cleared from the desk.
  Simple meaning: RAM = temporary working space. Fast, but cleared when power goes off or program closes.

- **Disk / Storage = the filing cabinet.**
  The cabinet holds files for a long time, even when power is off. Your documents, programs, and operating system live here.
  Simple meaning: Disk = long-term storage. Slower than RAM, but permanent.

- **Operating System (OS) = the office manager.**
  The manager decides who gets the desk, who gets the worker's time, who can open which filing cabinet drawer, and who must wait.
  Examples: Linux, Windows, macOS. In this roadmap, you will use Linux (Kali) in a lab, because most security work happens there.
  Simple meaning: OS = manager that controls hardware and gives programs a safe way to use it.

- **Programs / Applications = tasks given to the worker.**
  A browser, a terminal, a text editor — each is a program. When a program runs, the OS creates a **process** for it (see Section 4).

**Practical design note:**
A secure computer is designed like a secure office: not everyone can open every drawer, not every worker can do every job, and the manager keeps a log of who did what. When a red teamer later checks a system, they are really checking this design: Who can do what? What is logged? What is left open by mistake in a lab?

---

## 2. Hardware to software — the layers, simply

From bottom to top, like floors in a building:

```
Layer 5: User — you, typing and clicking
Layer 4: Applications — browser, terminal, editor
Layer 3: Operating System — Linux / Windows, the manager
Layer 2: Kernel — the core of the OS, talks directly to hardware
Layer 1: Hardware — CPU, RAM, disk, network card
```

- **Kernel:** The core part of the OS. It talks to hardware directly. Normal programs do not talk to hardware directly — they ask the kernel, and the kernel decides if it is allowed.
  Why this matters: If a program could talk to hardware without any check, it could read or change anything. The kernel is a security checkpoint.

- **Shell:** A program that lets you talk to the OS by typing commands. On Linux, the terminal + shell (like Bash) is your shell.
  Simple meaning: Shell = translator between your typed words and the OS.

- **User space vs Kernel space, simply:**
  - User space = where normal programs run, with limits.
  - Kernel space = where the kernel runs, with full power.
  - A normal program that wants full power must ask the kernel, and the kernel checks permission first.

**Practical design note:**
This layer design is why permissions matter later. A normal user in user space cannot just read kernel memory or change system files. They need permission, or a design weakness in a lab, to go higher. That idea — limited power by design, higher power only with permission — is the core of computer security.

---

## 3. Files, folders, and data — what a file really is

### 3.1 What is a file?

A file is data saved on disk, with a name, a location, an owner, and permissions.

Examples of files:
- A text file: `notes.txt` — contains text you wrote.
- A program file: `python3` — contains instructions the CPU can run.
- A settings file: `/etc/hostname` — contains the computer's name, in a lab system.
- A log file: `/var/log/syslog` (on some Linux systems) — contains a record of what the system did.

Every file has:
- **Name:** How you refer to it, like `notes.txt`.
- **Path:** Where it lives, like `/home/your-user/notes.txt`. Path = address of the file.
- **Type:** Text, program, image, etc. Linux does not force a type from the name alone, but names help humans.
- **Size:** How much disk space it uses, in bytes.
- **Owner and group:** Who owns it, and which group it belongs to.
- **Permissions:** Who can read, write, and execute it. Full detail is in `linux-filesystem.md`.
- **Times:** When it was created, changed, and last read. Useful for logs and audits.

### 3.2 What is a folder (directory)?

A folder is a special file that contains a list of other files and folders.

Think of folders as rooms in a building, and files as papers in those rooms. A path like `/home/your-user/projects/notes.txt` means:
- Start at root `/`
- Go into `home`
- Go into `your-user`
- Go into `projects`
- Find file `notes.txt`

### 3.3 Text vs binary, simply

- **Text file:** You can read it with `cat`. It contains readable characters: letters, numbers, symbols.
- **Binary file:** You cannot read it as plain text. It contains instructions or data in a form only a program understands, like a program file or an image file.
- **Practical rule:** Never open a binary file and guess its meaning. Use the right tool for that file type, in your own lab only. Never read private key files or other people's private files — checking permissions only is enough for an audit, as in your System Audit project.

### 3.4 File formats you will meet often

- `.txt` — plain text, simple notes.
- `.md` — Markdown, text with simple formatting for README files, like this repo.
- `.py` — Python program file.
- `.sh` — Shell script, a list of terminal commands saved in a file.
- `.log` — Log file, a record of events.
- `.conf` / files in `/etc` — Settings files, often plain text, that control how a program or system behaves.

**Practical design note:**
Settings files are powerful. Changing one line in a settings file can change how a whole service behaves. That is why professionals back up a settings file before changing it in a lab, and why they check settings files carefully in an audit — but only on their own machine, or in a lab they own.

---

## 4. Processes and services — what is running right now?

### 4.1 What is a process?

When a program runs, the OS creates a **process** for it.

A process is:
- A running program, with its own memory space on the desk (RAM).
- An ID number, called PID (Process ID).
- An owner (which user started it).
- A state: running, sleeping (waiting), stopped, etc.

Example, in simple words:
- You open a terminal. The OS creates a process for that terminal.
- You run `python3 password_check.py` in lab. The OS creates a process for Python.
- When Python finishes, that process ends, and its desk space is cleared.

### 4.2 What is a service (daemon)?

A service is a program that runs in the background, waiting to do a job, without a window you see.

Examples, in lab terms:
- A web server service waits for web requests on a port.
- A database service (like PostgreSQL) waits for database requests on port 5432, on localhost only in a safe lab design.
- A logging service writes events to log files in the background.

Simple meaning: Service = a worker who waits quietly in the background, ready to do a specific job when asked.

### 4.3 How to check processes on your own machine only

**Lab task — own machine only:**

```bash
ps aux | head -20
```

What this means, line by line:
- `ps` = list processes.
- `aux` = show all users' processes, with user, PID, CPU, memory, command.
- `| head -20` = show only first 20 lines, so output is readable. `|` means "send output of first command into second command".

Read one line and ask:
- Who owns this process? (USER column)
- What is its PID? (PID column)
- What command started it? (COMMAND column)
- Is this expected on my machine? If you do not recognise it, do not kill it blindly. Note it, and learn what it is from documentation first.

**Another lab check — own machine only:**

```bash
ss -tln
```

What this means:
- `ss` = socket statistics, network connections and listening ports.
- `-t` = TCP only.
- `-l` = listening only.
- `-n` = show numbers, not names, so you see actual ports.
- Output meaning: `127.0.0.1:5432` = service listening on localhost only, safer. `0.0.0.0:5432` = service listening on all networks, check this carefully in lab design.

You already used this in your System Audit project. That project is a practical example of this section: it checks OS, user, permissions, and localhost ports, and saves a report.

### 4.4 Practical design notes — processes and security

- **Least privilege design:** A process should run with only the permission it needs, not as full administrator/root, unless it really needs it. If a lab service runs as root and has a problem, the impact is bigger than if it runs as a limited user. That is why professionals check: "Which user runs this service, and why?"
- **Unexpected processes:** In a real job, an unexpected process is a clue, not proof. It could be a new program you installed, a service you forgot, or in a real incident, something malicious. In a lab, practise noting unexpected processes and learning what they are from documentation, before judging.
- **Logs + processes together:** A process tells you what is running now. A log tells you what happened before. Together, they tell a story. That is why Blue Teams watch both, and why Red Teams in labs should also learn what logs show.

---

## 5. Users, groups, and permissions — the short version

Full detail is in `linux-filesystem.md`. Here is the short, essential version you need before that file:

- **User:** A person or a service account that can log in and run programs. Examples: `your-user` (a human user in lab), `postgres` (a service account for a database, in lab).
- **Group:** A collection of users. Permissions can be given to a group at once, instead of to each user one by one.
- **Root / Administrator:** The superuser on Linux is called `root`. Root can do almost anything. That is why you must be careful with root: a mistake as root can break a system, and a lab service running as root has high impact if it has a problem.
- **Permissions, in one line:** For each file, three groups get three rights:
  - Owner: read (r), write (w), execute (x)
  - Group: read, write, execute
  - Others: read, write, execute
  - Numbers: r=4, w=2, x=1. Add them: 7 = rwx, 6 = rw-, 5 = r-x, 4 = r--, 0 = ---.
  - Example: `775` = owner 7 (rwx), group 7 (rwx), others 5 (r-x).

**Practical design note:**
Security design always asks three questions for any file or service:
1. Who needs to read this?
2. Who needs to change this?
3. Who should NOT see or change this?
If you cannot answer those three, the permission design is not finished yet.

---

## 6. Operating systems — Linux vs Windows, simply

You will meet both in red teaming. Learn both basics, in labs you own.

### Linux, simply
- Open, flexible, terminal-first.
- Filesystem starts at `/`, no drive letters like C:.
- Permissions are central, as above.
- Most servers and most security tools run on Linux.
- Kali Linux is a Linux system made for security learning, with many tools pre-installed.

### Windows, simply
- Common for office computers and company networks.
- Filesystem uses drive letters, like `C:\Users\...`.
- Users, groups, and Active Directory manage many computers together (see roadmap Phase 4).
- PowerShell is Windows' powerful terminal tool, like Bash on Linux, but for Windows.
- Event Logs are Windows' record of what happened, like Linux logs in `/var/log`.

### Why learn both?
A company network often has both: Linux servers + Windows office computers + Active Directory to manage them. A red teamer who only knows one side will miss half the network design. Start with Linux deeply, then learn Windows basics in your own lab VM, step by step.

---

## 7. Algorithm — How to understand any new computer system

Use this algorithm every time you meet a new system, lab VM, or service. It is the same logic you will use later for networks, web apps, and AD.

```
ALGORITHM: UNDERSTAND_A_SYSTEM

INPUT: A computer / VM / service in your own lab
OUTPUT: A one-page note you can explain

1. IDENTIFY
   - What OS is it? (Linux? Windows? Version?)
   - What is its hostname? What user are you?
   - Commands (own machine only): uname -a, hostname, whoami

2. MAP
   - What folders exist at top level? (ls / on Linux, in lab)
   - What users exist? (own lab only, check with safe commands your course guides)
   - What services are listening on localhost? (ss -tln, own machine only)

3. CHECK PERMISSIONS
   - For your project folder and one test file: ls -l
   - Write: owner, group, permissions number, and what it means in plain words.
   - Ask: Who can read? Who can change? Who should not?

4. CHECK PROCESSES
   - List processes: ps aux | head -20 (own machine only)
   - Note 3 processes you recognise, and 1 you do not. Learn the unknown one from docs.

5. CHECK LOGS (lab only, own machine)
   - Find one log file in your lab, read last 20 lines, note what events it records.
   - Do NOT read other people's private logs. Your own lab logs only.

6. DRAW IT
   - Draw a simple diagram: CPU, RAM, Disk, OS, Applications, Users.
   - Label: where does data live? Where does it run? Who controls what?

7. EXPLAIN IT
   - Explain the system in 3 minutes, in simple English, without notes.
   - Structure: "This system is ______. It runs ______. Users are ______. Services listening are ______. Permissions are designed like ______."
   - If you cannot explain a part, that part goes back to Step 1.

END ALGORITHM
```

**Practical design note:**
This algorithm is not just for beginners. Professionals use the same steps, faster, on every new engagement in a lab or with permission: identify, map, check permissions, check processes, check logs, draw, explain. Speed comes from practice, not from skipping steps.

---

## 8. Lab tasks — do these on your own machine only

Do each task, save output in notes, and explain it. Do not skip the explanation step — that is where learning happens.

**Task 1 — Identify your system:**
```bash
uname -a
hostname
whoami
pwd
```
Write in notes: OS name, hostname, current user, current folder, and what each command means.

**Task 2 — Map top level:**
```bash
ls /
ls -l /home
```
Write: What folders exist at root? What is in `/home`? Which folder is yours?

**Task 3 — Check a process list:**
```bash
ps aux | head -20
```
Write: 3 processes you recognise, what they do, and 1 process you will learn about from docs.

**Task 4 — Check localhost ports:**
```bash
ss -tln
```
Write: Each listening port, what service it likely is, and whether it is localhost-only (`127.0.0.1` / `::1`) or exposed (`0.0.0.0`). For localhost-only, write "safer by design". For exposed, write "check this in lab design".

**Task 5 — Make a test file, check it:**
```bash
mkdir -p ~/lab-test
echo "This is a test file I made myself." > ~/lab-test/test.txt
ls -l ~/lab-test/test.txt
cat ~/lab-test/test.txt
```
Write: File name, path, size, owner, permissions, and content meaning. This is test data only — never use real private data for this task.

**Task 6 — Draw and explain:**
Draw on paper or in notes: CPU, RAM, Disk, OS, Applications, Users, with arrows showing how data moves. Then explain your drawing in 2 minutes, out loud, in simple English. Record it if you can — that recording is also your English practice.

---

## 9. Common mistakes — and how to fix them

- **Mistake: Treating RAM and Disk as the same.**
  Fix: RAM = desk, temporary, cleared on close/power off. Disk = filing cabinet, permanent. If you save a file, it goes to disk. If you just open a program, it uses RAM.

- **Mistake: Thinking a process and a program are the same.**
  Fix: Program = instructions saved on disk. Process = program running right now, with memory and PID. One program can have many processes running at once.

- **Mistake: Killing a process you do not recognise, just to "clean up".**
  Fix: Note it, learn what it is from documentation first, then decide. Killing system processes on your own machine can break your lab session, and in a real job, killing the wrong process can break a service.

- **Mistake: Running everything as root because it is easier.**
  Fix: Use a normal user first. Use root only when you understand why that command needs it, and only in your own lab. Ease now creates bad habits later.

- **Mistake: Ignoring error output, and trying the same command again.**
  Fix: Read the full error, copy exact text into notes, and use the Debug Algorithm from `01-roadmap/how-to-learn.md`.

- **Mistake: Memorising definitions without doing lab tasks.**
  Fix: For each definition in this file, do the matching lab task today. Definition + lab + explanation = learning. Definition alone = forgetting tomorrow.

---

## 10. Checklist — can you move to Linux Filesystem?

Do not move to the next file until you can check these boxes honestly:

- [ ] I can explain CPU, RAM, Disk, OS, Kernel, Shell, Program, Process, Service, File, Folder, User, Group, in simple English, without reading definitions word for word.
- [ ] I can draw the layers: User -> Applications -> OS -> Kernel -> Hardware, and explain each layer.
- [ ] I ran `uname -a`, `hostname`, `whoami`, `pwd`, `ls /`, `ps aux`, `ss -tln` on my own machine, and I can explain each output.
- [ ] I made a test folder and test file myself, checked its permissions with `ls -l`, and explained them.
- [ ] I can explain localhost-only vs exposed ports, with the PostgreSQL 5432 example from lab terms.
- [ ] I recorded a 2-minute explanation of "How a computer works" in simple English.

If a box is unchecked, that topic goes back through the Learning Algorithm from `how-to-learn.md`. That is not slow — that is structured.

**Next file:** `linux-filesystem.md` — the full filesystem, every important folder, permissions in depth, and how to audit a filesystem safely on your own machine only.

---

*Educational use only. All practice must be on systems you own or have written permission to test.*
