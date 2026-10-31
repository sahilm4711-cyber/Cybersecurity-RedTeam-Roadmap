# Computer Basics — What Actually Matters

Lab only. Everything here is done on your own machine or your own private lab VM, with test files you made yourself. Nothing in this file is about anyone else's system.

If you can't explain a machine in plain language and check it with your own hands, the tools later on won't save you. Tools just automate what you already understand.

## The machine in one picture

A computer is a small office, and the analogy holds up better than most people expect.

The CPU is the worker. It runs one instruction at a time, very fast. Open a program and the CPU runs its instructions; close it and that work stops.

RAM is the worker's desk. It holds what is being used right now. Power off, or close the program, and the desk clears. That is why unsaved work vanishes and why a running program disappears from memory when it exits.

Disk is the filing cabinet. It keeps files when the power is off. Your documents, programs, and the operating system itself live there. Disk is slower than RAM and far more permanent.

The operating system is the office manager. It decides who gets desk space, who gets the worker's time, and who can open which drawer. Linux, Windows, macOS — same job, different rules. This roadmap leans on Linux (Kali in a lab) because that is where most of this work happens.

A program is a set of instructions on disk. When it runs, the OS turns it into a process — the same program, now alive, with its own memory and an ID number. One program can have several processes running at once. Confusing the two is a beginner error that keeps coming back, so keep them separate in your head: program on disk, process in memory.

Security, at this level, is just office discipline. Who can open which drawer? Who can do which job? What gets logged? When something goes wrong in a lab, it is almost always one of those questions with a bad answer.

## Layers: who talks to whom

From the hardware up:

```
User
Applications — browser, terminal, editor
Operating system
Kernel — the core of the OS, talks directly to hardware
Hardware — CPU, RAM, disk, network card
```

The kernel matters because normal programs do not touch hardware directly. They ask the kernel, and the kernel checks permission first. That checkpoint is the whole security model in miniature: user space runs with limits, kernel space runs with full power, and crossing between them requires permission.

The shell is the translator between your typed words and the OS. On Linux that is usually Bash in a terminal. Get comfortable there early — the graphical tools come and go, the shell stays.

## Files, folders, and data

A file is data on disk with a name, a location, an owner, and permissions. That is it. Everything else — size, timestamps, type — is metadata hanging off that definition.

A folder is a special file that lists other files and folders. A path is just an address in that tree. `/home/your-user/projects/notes.txt` means: start at root `/`, go into `home`, then `your-user`, then `projects`, and there is `notes.txt`.

Text files you can read with `cat`. Binary files you can't — a program, an image, a compiled tool. Don't `cat` a binary and guess. Use the right tool for the file type, and in an audit, checking a file's permissions is usually enough. You don't need to read private contents to learn that a file is too open.

Formats you will meet constantly: `.txt` for plain notes, `.md` for Markdown docs like this repo, `.py` for Python, `.sh` for shell scripts, `.log` for records of events, and files under `/etc` for settings. Settings files deserve respect. One changed line can change how a whole service behaves, which is why you back one up before touching it in a lab, and why auditors read them carefully — on their own machines.

## Processes and services

A process is a running program: its own memory, a PID, an owner, and a state (running, sleeping, stopped). Open a terminal and the OS creates a process for it. Run a Python script in a lab and the OS creates a process for Python. When it finishes, the process ends and its memory is freed.

A service, or daemon, is a program that runs in the background waiting for a job. A web server waits for web requests. A database service waits for database requests — in a safe lab design, on localhost only. A logging service writes events to log files without you ever seeing a window.

Check what is running on your own machine:

```bash
ps aux | head -20
```

`ps` lists processes, `aux` shows all users with owner, PID, CPU, memory, and command, and `| head -20` keeps the output readable by showing only the first twenty lines. Read one line at a time: who owns it, what is its PID, what command started it, and is it expected on this machine? If you don't recognise something, note it and look it up in documentation before you touch it. Killing a process you don't understand is how you break your own lab session.

Check what is listening:

```bash
ss -tln
```

`ss` shows sockets, `-t` is TCP only, `-l` is listening only, `-n` shows numbers instead of names so you see real ports. The output tells you a story: `127.0.0.1:5432` means a service listening on localhost only — safer by design, reachable only from this machine. `0.0.0.0:5432` means it is listening on all networks, and in a lab that is something to check and justify, not ignore.

A process tells you what is running now. A log tells you what happened before. Together they tell a story, which is why defenders watch both — and why anyone testing a lab should expect their actions to show up in logs too.

## Users, groups, and permissions — the short version

Full depth is in `linux-filesystem.md`. The essentials:

A user is a person or a service account that can log in and run programs. A group is a collection of users, so permissions can be handed out once instead of person by person. Root is the Linux superuser and can do almost anything, which is exactly why you don't run everything as root: a mistake as root breaks more, and a lab service running as root has a bigger blast radius if something goes wrong.

For each file, three groups get three rights: owner, group, and others, each with read (r), write (w), and execute (x). As numbers, r=4, w=2, x=1, so 7 is rwx, 6 is rw-, 5 is r-x, 4 is r--, and 0 is nothing. `775` means owner rwx, group rwx, others r-x.

Before you change any permission, answer three questions: who needs to read this, who needs to change this, and who should not see or change it at all? If you can't answer, the design isn't finished.

## Linux and Windows, briefly

Learn Linux deeply first, then Windows basics in your own lab VM. The split in real networks is usually Linux servers plus Windows office machines managed together, and knowing only one side means missing half the picture.

Linux is terminal-first, its filesystem starts at `/` with no drive letters, and permissions are central. Windows uses drive letters like `C:\Users\...`, manages many machines together through users, groups, and directory services, and uses PowerShell the way Linux uses Bash. Windows Event Logs play the same role Linux logs in `/var/log` do: a record of what happened.

## Sizing up a new box

Every new lab VM or service gets the same treatment, and with practice it gets fast — not because you skip steps, but because you stop fumbling them.

Start with identity. What OS and version is this, what user are you, where are you standing?

```bash
uname -a
hostname
whoami
pwd
```

Then map it. What sits at the top level, what is in your home folder, what services are listening on localhost — `ls /`, `ls -l /home`, `ss -tln`, all on your own machine.

Then check permissions on your project folder and one test file with `ls -l`, and read them back in plain words: who can read, who can change, who should not. Then check processes with `ps aux | head -20` and note three you recognise and one you don't, and learn the unknown one from docs rather than guessing.

Then find a log file in your own lab, read its last twenty lines, and note what kinds of events it records. Your own lab logs only — never anyone else's.

Finally, draw it. CPU, RAM, disk, OS, applications, users, with arrows for where data lives, where it runs, and who controls what. Then explain the drawing out loud in a few minutes without notes. The part you can't explain is the part you go back and redo. That is not slow work. That is the work.

## A lab pass you can actually do

Work in a throwaway folder so mistakes stay cheap:

```bash
mkdir -p ~/lab-test
echo "This is a test file I made myself." > ~/lab-test/test.txt
ls -l ~/lab-test/test.txt
cat ~/lab-test/test.txt
```

Read the `ls -l` output back: name, path, size, owner, permissions, and what they mean. Then run `ps aux | head -20` and `ss -tln` and write down, for each listening port, whether it is localhost-only or exposed, and what service it likely is.

Watch out for the habits that cost beginners the most time: treating RAM and disk as the same thing, running everything as root because it is easier, killing a process before learning what it is, and ignoring error output and retrying the same command. Read the full error, copy it into your notes, and work from what it actually says.

When you can explain CPU, RAM, disk, OS, kernel, shell, program, process, service, file, folder, user, and group in plain English, and you can run the checks above on your own machine and explain every line of output, move on to `linux-filesystem.md`.

---

*Lab only: systems you own or have written permission to test, test data only.*
