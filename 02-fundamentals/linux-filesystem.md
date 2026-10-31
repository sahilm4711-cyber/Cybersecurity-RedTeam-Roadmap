# 02 — Fundamentals: Linux Filesystem, Fully Explained

## What this file is

This is Phase 1, part 2, and one of the most important files in this whole roadmap.

If you understand the Linux filesystem deeply — every important folder, what lives there, what permissions mean, and how to audit it safely — you will understand later topics faster: networks, web servers, system audits, and even Active Directory concepts, because they all follow the same idea: who can read, who can change, who should not.

No shortcuts here. Each folder gets: what it is, what lives there, an example, what happens if you change it, and a security design note. Permissions get full depth, not just numbers to memorise.

**Safety rule for this whole file:** All commands are on your own machine only, with test files you made yourself. Never change system folders you do not understand, never read other people's private files, and never change permissions on a real system without permission and a backup plan. In a lab, practise on test folders first.

---

## 1. The filesystem as a building — the mental model

Think of Linux filesystem as a building with one entrance:

- **Entrance = `/` (root).**
  Everything starts here. There is no C: drive, no D: drive like Windows. One tree, starting at `/`, branching into folders.

- **Rooms = folders.**
  Each folder has a purpose, designed by Linux itself. You do not invent new top-level folders for fun — you use the designed rooms for their designed purpose.

- **Papers = files.**
  Files live in rooms. A file's full path is its address in the building: `/home/professor/projects/notes.txt` means: entrance -> home room -> professor room -> projects room -> notes.txt paper.

- **Keys = permissions.**
  Each room and each paper has keys: who can read it, who can change it, who can enter the room. Permissions are the key system of the building.

**Practical design note:**
Linux designers separated folders by purpose for security and for order:
- User files separate from system files, so a user mistake does not break the system.
- Settings separate from logs, so you can check settings without mixing them with history.
- Temporary files separate from permanent files, so temporary mess does not pollute permanent data.
When you later audit a system, you are really checking: "Is this separation still intact in this lab? Or has someone mixed things, opened keys too wide, or left temporary files with sensitive data?"

---

## 2. The root and every important folder — full table first, detail after

| Folder | Simple meaning | What lives there | Example |
|--------|----------------|------------------|---------|
| `/` | Root, top of everything | All other folders | `ls /` shows all top folders |
| `/home` | Users' personal folders | Each user's home, like `/home/professor` | Your projects, notes, test files |
| `/root` | Root user's home | Root's personal files only, not for normal users | Only root can normally enter |
| `/etc` | System settings | Settings files for system and services | `/etc/hostname`, `/etc/passwd` (see note below) |
| `/var` | Changing data | Logs, caches, service data that changes often | `/var/log` — system logs |
| `/tmp` | Temporary files | Short-lived files, cleared on reboot often | Test files you do not need to keep |
| `/usr` | User programs and data | Programs, libraries, documentation for users | `/usr/bin/python3` |
| `/bin` | Essential commands | Basic commands needed to run the system | `ls`, `cat`, `cp` live here or linked here |
| `/sbin` | System admin commands | Commands for system management, often for root | `ip`, `reboot` (in lab, use carefully) |
| `/opt` | Optional software | Extra software installed by hand, not by system packages | Some lab tools installed manually |
| `/boot` | Boot files | Files needed to start the computer | Kernel files, bootloader settings — do not change without understanding |
| `/dev` | Devices | Device files, like disks, terminals, as files | `/dev/null` — a special file that discards data |
| `/proc` | Process info (virtual) | Not real files on disk, but live info about processes and kernel | `/proc/cpuinfo` — CPU info, read-only view |
| `/sys` | System info (virtual) | Live info about hardware and kernel, like `/proc` but for devices | Hardware info, read-only view mostly |
| `/mnt` and `/media` | Mount points | Where external drives or other filesystems are attached temporarily | USB drive mounted here in lab, if you use one |

Now, detail for each important folder, one by one. Do not rush this section — this is the core of the file.

---

## 3. Folder by folder — what, example, what if you change it, security note

### 3.1 `/` — Root

- **What:** Top folder. Everything else is inside `/`.
- **Example:** `ls /` on your own machine lists: `bin`, `boot`, `dev`, `etc`, `home`, `lib`, `media`, `mnt`, `opt`, `proc`, `root`, `run`, `sbin`, `srv`, `sys`, `tmp`, `usr`, `var` (exact list varies by system, that is normal).
- **What if you change it:** Do not create random folders at `/` in a real system. Top-level design is for the OS. In your lab, practise in `/home` or a test folder, not at `/`.
- **Security note:** If a user can write to `/` freely, the whole building design is broken. Check permissions of `/` in an audit: it should be owned by root, not writable by everyone.

### 3.2 `/home` — Users' homes

- **What:** Each normal user gets a folder here. Example: user `professor` gets `/home/professor`. That is your personal room in the building.
- **What lives there:** Your documents, projects, notes, downloads, settings for your user only (hidden files starting with `.`, like `.bashrc`).
- **Example lab commands (own machine only):**
  ```bash
  ls -l /home
  ls -l ~
  pwd
  ```
  `~` means "my home folder". `pwd` shows where you are now. If you are in `/home/professor`, `pwd` prints that path.
- **What if you change it:** Your home is yours to organise in lab. Create `projects`, `notes`, `lab-test` here. Do not delete another user's home, and do not enter another user's home without permission.
- **Security note:** Home folders should not be readable by everyone if they contain private notes or keys. Check with `ls -ld /home/professor` in your own lab, and understand what the permission letters mean (Section 5).

### 3.3 `/root` — Root's home

- **What:** Home folder for the root superuser only.
- **Example:** Normal users cannot normally enter `/root`. That is by design.
- **What if you change it:** Do not practise here as a normal user. If you need root in lab, understand why first, and return to normal user after.
- **Security note:** If a normal user can read `/root`, that is a serious permission design problem in lab terms. Note it in an audit, do not exploit it on a real system.

### 3.4 `/etc` — Settings (the control room)

- **What:** System-wide settings files. Think: control room of the building.
- **What lives there:**
  - `/etc/hostname` — computer name, in simple text.
  - `/etc/hosts` — local name-to-IP mapping, used before DNS in some cases.
  - `/etc/passwd` — list of users, with user ID, group ID, home folder, and shell. Note: on modern Linux, passwords are NOT stored here in plain text — they are stored in a protected shadow file. Do not try to read shadow files. For an audit, checking that `/etc/passwd` exists and its permissions is enough.
  - `/etc/shadow` — protected password hashes, root only. **Do not read this file, do not copy it, do not share it.** In this roadmap, you will never be asked to read shadow contents. Checking its permissions only (should be root-only) is the safe audit practice.
  - Service settings: many services have a folder or file here, like `/etc/ssh/` for SSH settings, if SSH is installed in lab.
- **Example lab commands (own machine only, read-only for settings you understand):**
  ```bash
  ls -l /etc/hostname
  cat /etc/hostname
  ls -l /etc/
  ```
  `cat` prints a text file. Only `cat` files you understand and are allowed to read in your own lab.
- **What if you change it:** Changing a settings file can change how a whole service behaves, or break login. In lab, back up first: copy the file to a test name before changing, and change only one line at a time, with a note of what you changed and why.
- **Security note:** Settings files are a top audit target in lab terms: Are default passwords changed in lab? Are unnecessary services disabled? Are permissions on settings files too open? Those questions are design questions, not just file checks.

### 3.5 `/var` — Changing data, especially logs

- **What:** Data that changes often: logs, caches, mail spools, service data.
- **What lives there:**
  - `/var/log` — logs. Logs are history books: what happened, when, who did what, what failed.
  - `/var/tmp` — temporary files that survive reboot, unlike `/tmp` in some systems.
  - Service data: for example, a database might store its data under `/var/lib/` in lab, depending on setup.
- **Example lab commands (own machine only):**
  ```bash
  ls -l /var/log | head -20
  ```
  Read only log file names and permissions first. Do not read other people's private logs. Your own lab logs are for learning how logs look.
- **What if you change it:** Do not delete logs to "clean up" in a real job context. Logs are evidence. In a lab, you can rotate or clear test logs you created yourself, but understand: deleting logs hides history, and in a real incident, that is a serious problem.
- **Security note:** Log design matters: Are important events logged? Are logs protected from being changed by normal users? Are logs checked regularly? A system with no logs, or logs anyone can change, is hard to defend. That is a Blue Team lesson, and a Red Team lab lesson too: a good test should be detectable in logs.

### 3.6 `/tmp` — Temporary files

- **What:** Short-lived files. Programs use `/tmp` for temporary work. Often cleared on reboot.
- **Example:** A program might write a temporary file here while it runs, then delete it when done.
- **What if you change it:** In lab, you can create test files here, but do not store important notes here — they may disappear. Use `/home` for important work.
- **Security note:** Temporary files can be a security risk in lab design if they contain sensitive data and are readable by everyone, or if a program trusts a temporary file without checking who made it. In your own lab, check permissions of test files you create in `/tmp`, and do not put private data there.

### 3.7 `/usr` — User programs and data

- **What:** Programs, libraries, and documentation for normal users, installed by the system.
- **What lives there:**
  - `/usr/bin` — most user commands, like `python3`, `grep`, `ls` (on modern systems, often linked from `/bin`).
  - `/usr/share` — shared data, like documentation, icons, examples.
  - `/usr/lib` — libraries programs need to run.
- **Example:**
  ```bash
  which python3
  ls -l $(which python3)
  ```
  `which` shows where a command lives. `$(...)` means "run this command and use its output here". Read output slowly: path, permissions, owner.
- **What if you change it:** Do not change files in `/usr` by hand in lab, unless a course guides you and you understand why. System updates manage `/usr`. Your work belongs in `/home`, not in `/usr`.
- **Security note:** If a normal user can change a program in `/usr/bin`, that program could be changed for everyone. That is why `/usr` should be owned by root and not writable by normal users. Check that in an audit.

### 3.8 `/bin` and `/sbin` — Essential commands

- **What:** `/bin` = basic commands needed for the system to run, for all users. `/sbin` = system administration commands, often for root, like managing disks, networks, and booting.
- **Example:** `ls`, `cat`, `cp`, `mv` are in `/bin` (or linked there). `ip`, `reboot` are system commands — use `reboot` only if you really mean to restart your own lab VM, and never on a system you do not own.
- **Security note:** Same as `/usr`: these folders should be root-owned, not writable by normal users, by design.

### 3.9 `/opt` — Optional, hand-installed software

- **What:** Software installed manually, outside the normal system packages.
- **Example:** A lab tool you download and install by hand might live in `/opt/toolname`.
- **What if you change it:** In lab, you can install test tools here if a course guides you, but note what you installed, when, and why, in your notes. Untracked software is hard to audit later.
- **Security note:** Untracked software in `/opt` is an audit question: "What is this? Who installed it? Is it still needed? Is it updated?" Good lab design tracks every manual install.

### 3.10 `/boot` — Boot files (do not touch without understanding)

- **What:** Files needed to start the computer: kernel image, bootloader settings.
- **Example:** You will see kernel files here, but you do not need to change them as a beginner.
- **What if you change it:** Changing boot files wrongly can stop a computer from starting. In lab, do not change `/boot` unless a specific, trusted course guides you, step by step, with a backup and a way to recover.
- **Security note:** Boot security (like secure boot, bootloader password in real systems) is an advanced topic. For now, remember: `/boot` is critical, root-owned, and not a practise folder.

### 3.11 `/dev`, `/proc`, `/sys` — Special, virtual folders

These are not normal folders with normal files on disk. They are views into the system, presented as files so programs can read them with normal file tools.

- **`/dev` — Devices as files:**
  - Example: `/dev/null` — a special file that discards anything written to it, like a black hole for output. Useful in commands: `command > /dev/null` means "throw away output, I do not need it".
  - `/dev/sda` (example name, varies) — a disk, presented as a file. Do not write to disk devices directly unless you fully understand what you are doing, and only in your own lab with a disposable test disk, never on your main disk.
- **`/proc` — Process and kernel info, live:**
  - Example: `cat /proc/cpuinfo` shows CPU info, read-only. `ls /proc` shows folders named by PID — each running process has a folder here.
  - Practical use in lab: check CPU, memory info, and process details, read-only, on your own machine only.
- **`/sys` — Hardware and kernel info, live:**
  - Example: information about devices, power, and kernel settings, presented as files.
  - Practical rule for beginners: read-only exploration in lab is fine. Do not change files in `/proc` or `/sys` to "tune" a system unless a trusted guide explains exactly what, why, and how to undo it.

**Practical design note:**
The idea of "everything is a file" in Linux is powerful: devices, processes, and kernel info can all be read (and sometimes changed) with file tools. That is elegant design, and also why permissions on these special folders matter: read-only for normal users, write only for root where needed, by design.

---

## 4. Paths — absolute, relative, and home, fully explained

### 4.1 Absolute path

Starts with `/`, means "from the root, exactly this address".

Example: `/home/professor/projects/notes.txt`
- No matter where you are now, this path means the same file.
- Use absolute paths when you want to be precise, in scripts, in reports, and when giving instructions to someone else.

### 4.2 Relative path

Does NOT start with `/`, means "from where I am now".

Example: If you are in `/home/professor`, then:
- `projects/notes.txt` means `/home/professor/projects/notes.txt`
- `../` means "go up one folder" — from `/home/professor/projects`, `../` means `/home/professor`
- `./` means "here, current folder" — `./notes.txt` in `/home/professor` means `/home/professor/notes.txt`
- `..` twice: `../../` means go up two folders.

**Lab example — own machine only:**
```bash
pwd
cd ~/lab-test
pwd
cd ..
pwd
cd -
pwd
```
What each means:
- `pwd` = print working directory, where am I now?
- `cd ~/lab-test` = go to lab-test folder in my home.
- `cd ..` = go up one folder.
- `cd -` = go back to previous folder you were in.

**Common mistake:** Using a relative path when you thought you were in a different folder, and then creating or deleting the wrong file. Fix: always run `pwd` and `ls -l` before a create, move, or delete command, especially in a new terminal.

### 4.3 Home shortcut `~` and variables

- `~` = your home folder. `~/notes.txt` = `/home/<your-user>/notes.txt`.
- `~professor` (example) = home folder of user `professor`, if you are allowed to see it in lab. Do not use this to peek into other users' homes without permission.
- `$HOME` is a variable that holds your home path. `echo $HOME` prints it. Variables are covered deeper in Linux commands file, but remember: `~` and `$HOME` usually mean the same place for you.

---

## 5. Permissions — full depth, no memorising without meaning

This is the most important security section in this file. Read it slowly, do the lab tasks, and do not move on until you can break any permission number into owner/group/others and explain it.

### 5.1 The permission string — reading `ls -l` output

Run on your own machine only:
```bash
ls -l ~/lab-test/test.txt
```

Example output (yours may differ):
```
-rw-r--r-- 1 professor professor 38 Oct 31 12:00 /home/professor/lab-test/test.txt
```

Break it, left to right:
- `-` (first character) = file type. `-` = regular file. `d` = directory/folder. `l` = link.
- `rw-` (next 3) = owner permissions.
- `r--` (next 3) = group permissions.
- `r--` (last 3) = others permissions.
- `1` = number of links (for files, usually 1; for folders, more — not critical for beginners).
- `professor` = owner user.
- `professor` = owner group.
- `38` = size in bytes.
- `Oct 31 12:00` = last change time.
- Path = file name and location.

So `-rw-r--r--` means:
- File, owner can read + write, group can read only, others can read only.
- Owner cannot execute it (no `x`), because it is a text file, not a program. That is correct design for a text file.

For a folder, example:
```
drwxrwxr-x 2 professor professor 4096 Oct 31 12:00 lab-test
```
- `d` = directory.
- `rwx` owner = read, write, execute. For a folder, execute means "enter the folder and access files inside it".
- `rwx` group = read, write, execute.
- `r-x` others = read, execute (enter and list), but NOT write (cannot create/delete files inside).
- This is `775` in numbers (see below).

### 5.2 Letters: r, w, x — what they mean for files vs folders

| Letter | For a file | For a folder |
|--------|------------|--------------|
| r (read) | Can read file content | Can list files inside folder (see names) |
| w (write) | Can change file content | Can create, delete, rename files inside folder |
| x (execute) | Can run file as a program (if it is a program) | Can enter folder and access files inside it (if you know their names and have permission on the file itself) |

**Critical nuance, in simple words:**
- For a folder, you need `x` to enter it, even if you have `r`. Without `x`, you cannot reach files inside, even if you know their names.
- For a folder, you need `w` to create or delete files inside, even if you do not have `w` on the file itself. Deleting a file depends on folder permission, not just file permission. That surprises beginners, and it matters for audits.
- For a file, `x` does not mean "open it". It means "run it as a program". A text file should usually NOT have `x`. A script you intend to run should have `x` for the user who will run it, in your own lab only.

### 5.3 Numbers: octal permissions — how to calculate, not just memorise

Each letter has a number:
- r = 4
- w = 2
- x = 1
- - = 0

Add for each group (owner, group, others):

Examples, step by step:
- `rwx` = 4 + 2 + 1 = 7
- `rw-` = 4 + 2 + 0 = 6
- `r-x` = 4 + 0 + 1 = 5
- `r--` = 4 + 0 + 0 = 4
- `---` = 0

Now combine three groups:
- `rwxrwxr-x` = owner 7, group 7, others 5 = **775**
- `rw-r--r--` = owner 6, group 4, others 4 = **644**
- `rw-------` = owner 6, group 0, others 0 = **600**
- `rwxr-xr-x` = owner 7, group 5, others 5 = **755**

**Common permission numbers, with design meaning:**

| Number | Meaning | When is it good design? | When is it risky? |
|--------|---------|-------------------------|-------------------|
| 600 | Owner read/write only, no one else | Private file, like personal notes, private test data in lab | If a service needs to read it but cannot, service will fail — check design, do not just open it to everyone |
| 644 | Owner read/write, group read, others read | Public text file, README, normal document in lab | If file contains private data, 644 is too open — others can read it |
| 755 | Owner full, group read+enter, others read+enter | Program or folder that everyone should use, but only owner should change | If folder contains private files, folder 755 alone does not protect files inside — check file permissions too |
| 775 | Owner full, group full, others read+enter | Shared project folder for a team/group in lab, where group members should also change files | If group is large or includes users who should not change files, 775 is too open for group write |
| 777 | Everyone full: read, write, execute/enter | Almost never good design. Only in a disposable test folder, for a few minutes, in your own lab, with test data only, and changed back after | Almost always risky: anyone can change or delete. Never use 777 for real files, system files, or folders with important data |

**Practical design note — never use 777 as a fix:**
Beginners, when a program says "permission denied", often run `chmod 777` to "fix" it quickly. That is a shortcut, and a bad design. It fixes the symptom by opening the file to everyone, which creates a security problem.
Correct design algorithm for "permission denied" in your own lab:
1. Who owns the file? (`ls -l`)
2. Who are you? (`whoami`, `groups`)
3. What permission do you actually need? Read? Write? Execute/enter?
4. Change only that permission, for only that group (owner/group/others), to the minimum needed.
5. Test again. If it works, you fixed it with minimum opening. If not, check folder permissions too (you need `x` to enter a folder, and `w` on folder to create/delete inside).

### 5.4 Changing permissions safely in lab — `chmod` and `chown` (concept)

**`chmod` — change mode (permissions):**

Lab examples, test files only, own machine only:
```bash
mkdir -p ~/lab-test
echo "test" > ~/lab-test/a.txt
ls -l ~/lab-test/a.txt
chmod 600 ~/lab-test/a.txt
ls -l ~/lab-test/a.txt
chmod 644 ~/lab-test/a.txt
ls -l ~/lab-test/a.txt
```

What happens each time:
- First `ls -l` shows original permissions, often 644.
- After `chmod 600`, owner only can read/write, group and others get `---`.
- After `chmod 644`, back to owner read/write, others read.
- Always `ls -l` before and after, so you see the change. That is the audit habit.

Symbolic form (also useful, not required to memorise all):
- `chmod u+x file` = add execute for user/owner.
- `chmod g-w file` = remove write for group.
- `chmod o-r file` = remove read for others.
- `u` = user/owner, `g` = group, `o` = others, `a` = all.

**`chown` — change owner (advanced, lab only, careful):**
- `chown` changes who owns a file. Normally only root can change owner to another user, in lab.
- Beginners: understand the concept (owner matters for permissions), but do not practise `chown` on system files. Practise concept only on test files you own, if a course guides you, and only in your own lab.
- Practical note: If you change owner wrongly in a real system, the original owner may lose access. That is why `chown` is a careful command, not a daily shortcut.

**Umask — default permissions, simply:**
- When you create a new file, Linux does not ask you for permissions each time. It uses a default, controlled by `umask`.
- Simple idea: `umask` subtracts permissions from the maximum. For files, maximum is usually 666, for folders 777, then umask removes some.
- Example: If umask is `022`, new files often become 644 (666 - 022 = 644), new folders become 755 (777 - 022 = 755). You do not need to calculate umask perfectly as a beginner, but remember: defaults exist by design, and you can check your umask with `umask` command in lab, and see its effect by creating a test file and running `ls -l` on it.
- Security design note: A safe umask for private work is more restrictive, like `077` (new files 600, folders 700). A shared work umask is less restrictive, like `022`. Choose by design: private vs shared, not by habit.

### 5.5 Ownership and groups — why they matter for red teaming

- A file owned by root, with permissions 644, can be read by everyone, but changed only by root. That is good design for system files everyone needs to read.
- A file owned by a service user (like a database user in lab), with permissions 600, can be read/changed only by that service. That is good design for private service data.
- A folder owned by a group, with permissions 775, allows group members to work together, but others only to read/enter. That is good design for a shared project folder in lab, if group membership is controlled.
- **Red-team audit question, in lab terms:** "Is this file owned by the right user? Is its group correct? Are permissions minimum needed, or too open?" If a private file is 644, or a system file is writable by everyone, that is a finding in lab terms — with a fix idea: change to minimum needed, and check why it was open.

---

## 6. Algorithm — How to audit a filesystem safely (own machine only)

Use this algorithm for your System Audit project, and for any filesystem check in a lab. Do not use it on other people's machines.

```
ALGORITHM: AUDIT_FILESYSTEM (own machine only)

INPUT: A folder to audit, in your own lab, for example ~/lab-test or your project folder
OUTPUT: A report you can explain

1. IDENTIFY
   - Run: pwd, ls -l
   - Write: Where am I? What folder am I auditing? Who am I? (whoami)
   - Do NOT audit a folder you do not own, or a system folder you do not understand yet, without a guide.

2. LIST TOP LEVEL
   - Run: ls -ld <folder>   (for the folder itself)
   - Run: ls -l <folder>    (for files inside)
   - For each item, note: type (file/folder), owner, group, permissions number, size.

3. CHECK PERMISSIONS, ONE BY ONE
   - For each file/folder, break permissions into owner/group/others.
   - Ask three design questions:
     a) Who needs to read this?
     b) Who needs to change this?
     c) Who should NOT read or change this?
   - If actual permissions are wider than needed, note it as a lab finding, with fix idea:
     Example: "Test file a.txt is 644, but contains private test notes. Fix idea: chmod 600, because only owner needs it."

4. CHECK OWNERSHIP
   - Is owner correct for this file's purpose?
   - Is group correct?
   - If a system file is owned by a normal user in lab, note it — that is unusual design, check why.

5. CHECK SPECIAL FOLDERS
   - If auditing a system folder in lab (with a guide), check: /etc permissions, /var/log permissions, /tmp test files.
   - Do NOT read shadow files, private keys, or other users' private files. Permissions check only is enough.

6. CHECK FOR RISKS IN LAB TERMS
   - Any file with 777? Note it, fix idea: change to minimum needed.
   - Any private file with 644 or wider? Note it.
   - Any folder writable by others that should not be? Note it.
   - Any unexpected file you do not recognise? Note it, learn what it is from docs, do not delete blindly.

7. WRITE REPORT
   - Save report as audit_report.txt in your lab folder, with date, folder audited, findings, and fix ideas.
   - Remove private details before sharing or publishing any report.

8. EXPLAIN IT
   - Explain the report in 3 minutes, in simple English, without reading it word for word.
   - Structure: "I audited ______. I found ______. The risk in lab terms is ______. The fix idea is ______."

END ALGORITHM
```

This algorithm is exactly what your System Audit Python project does in code: it checks system info, checks permissions with `os.stat`, checks localhost ports with `ss`, and saves a report. Now you understand the logic behind the code, not just the code itself.

---

## 7. Lab tasks — do these on your own machine only, with test files

**Task 1 — Explore root and home:**
```bash
ls /
ls -l /home
ls -ld ~
ls -l ~
```
Write: What top folders exist? What is in your home? What are permissions of your home folder itself (`ls -ld ~`)?

**Task 2 — Explore settings and logs (names and permissions only, own machine):**
```bash
ls -l /etc/hostname
cat /etc/hostname
ls -l /var/log | head -20
```
Write: What is your hostname? What log files exist? Do NOT read private log contents beyond what your course guides, and never read other users' private logs.

**Task 3 — Permission lab, test files only:**
```bash
mkdir -p ~/lab-test/perm
echo "hello lab" > ~/lab-test/perm/a.txt
ls -l ~/lab-test/perm/a.txt
chmod 600 ~/lab-test/perm/a.txt
ls -l ~/lab-test/perm/a.txt
chmod 644 ~/lab-test/perm/a.txt
ls -l ~/lab-test/perm/a.txt
mkdir -p ~/lab-test/perm/myfolder
ls -ld ~/lab-test/perm/myfolder
chmod 700 ~/lab-test/perm/myfolder
ls -ld ~/lab-test/perm/myfolder
```
Write: For each step, what number, what letters, and what it means for owner/group/others. Explain why 600 is private, 644 is public-readable, 700 for a folder is owner-only.

**Task 4 — Relative vs absolute paths:**
```bash
pwd
cd ~/lab-test
pwd
ls -l
cd perm
pwd
cd ..
pwd
cd -
pwd
```
Write: Each `pwd` output, and what `..`, `.`, `~`, `-` meant in that step.

**Task 5 — Filesystem audit, own project folder only:**
Run your System Audit idea manually first, then with your Python script if you have it:
- Check OS, user, current folder.
- Check permissions for `.` and `../` (or your project folder) with `ls -ld`.
- Check localhost ports with `ss -tln` (own machine only).
- Save a short report in notes, with findings and fix ideas, in your own words.
- Do NOT publish the report with private details without thinking first.

**Task 6 — Draw the filesystem:**
Draw on paper or in notes: `/` at top, branches to `/home`, `/etc`, `/var`, `/tmp`, `/usr`, `/bin`, `/opt`, `/boot`, with one example file in each, and one sentence for each folder's purpose. Then explain your drawing in 3 minutes, out loud, in simple English.

---

## 8. Common mistakes — and how to fix them

- **Mistake: Using `chmod 777` to fix "permission denied".**
  Fix: Use the design algorithm in Section 5.3: check owner, check who you are, check what permission you actually need, change only that, to minimum needed. 777 is almost never the correct fix.

- **Mistake: Deleting files with `rm -rf` without checking `pwd` and `ls` first.**
  Fix: Always `pwd`, `ls -l`, then delete, and only in a test folder you just listed. Never `rm -rf /` or `rm -rf ~` — those can destroy a system or your home. In lab, practise deletes only in `~/lab-test` with test files you made.

- **Mistake: Thinking folder read (`r`) alone lets you enter a folder.**
  Fix: For folders, you need `x` to enter. `r` alone lets you list names, but not access files inside without `x`. Test this in lab with a test folder: set 444 (r--r--r--) on a test folder, try to `cat` a file inside, note the error, then set 555 (r-xr-xr-x) and try again. That experiment teaches the difference better than memorising.

- **Mistake: Confusing file write with folder write for deleting.**
  Fix: Deleting a file needs write (`w`) on the folder, not just write on the file. Test in lab: create a test folder and file, remove write from file but keep write on folder, try to delete — it will usually succeed, because folder permission controls deletion. That is a key audit insight.

- **Mistake: Reading private files "just to learn".**
  Fix: For audits, permissions check only is enough. Do not read `/etc/shadow`, private keys, or other users' private files. If a file is private by design, respect that design, even in lab. Learning permissions does not require reading private contents.

- **Mistake: Creating important work in `/tmp` and losing it on reboot.**
  Fix: `/tmp` is temporary by design. Important work goes in `/home`, with backups or Git pushes for projects. Temporary work goes in `/tmp`, with the expectation it may disappear.

- **Mistake: Memorising permission numbers without breaking them into owner/group/others.**
  Fix: Every time you see a number, break it: 775 = 7,7,5 = rwx,rwx,r-x. Do that out loud until it is automatic. Numbers without meaning are forgetting tomorrow.

---

## 9. Checklist — can you move to Linux Commands?

Do not move to the next file until you can check these boxes honestly:

- [ ] I can draw the Linux filesystem from `/` down, with at least 10 important folders, and explain each folder's purpose in simple English.
- [ ] I can read any `ls -l` line and break it into type, owner, group, permissions, size, time, name, without hesitation.
- [ ] I can convert any permission letters to numbers and back: rwx=7, rw-=6, r-x=5, r--=4, ---=0, and combine for owner/group/others.
- [ ] I can explain, with lab examples, the difference between file r/w/x and folder r/w/x, especially why folder `x` is needed to enter, and why folder `w` controls deletion.
- [ ] I can explain why 777 is almost never correct, and what the correct minimum-permission fix is for "permission denied" in my own lab.
- [ ] I audited my own project folder (permissions + localhost ports, own machine only) and I can explain the report line by line.
- [ ] I recorded a 3-minute explanation of "Linux filesystem and permissions" in simple English.

If a box is unchecked, repeat the Learning Algorithm from `01-roadmap/how-to-learn.md` for that topic, with lab tasks from Section 7. That is structured, not slow.

**Next file:** `linux-commands.md` — daily Linux commands, each with purpose, lab example, output meaning, and common mistakes, in the same big, structured style. No shortcuts.

---

*Educational use only. All practice must be on systems you own or have written permission to test. Never read private key contents, shadow passwords, or other users' private files — permissions check only is enough for an audit.*
