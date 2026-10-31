# Linux Filesystem — The Layout, and Why Permissions Are the Job

Lab only, own machine only, test files you made yourself. Never change a system folder you don't understand, never read anyone else's private files, and never change permissions on a system you don't own. In a lab, practise on test folders first.

This is the file that pays for itself. Networks, web servers, system audits — they all come back to the same question: who can read this, who can change this, and who should not.

## The building

Linux has one tree, starting at `/`. No drive letters. Everything hangs off that root.

Think of it as a building with one entrance. Folders are rooms, each with a designed purpose — you don't invent new top-level rooms for fun, you use the rooms for what they were built for. Files are papers in those rooms. A full path is an address: `/home/your-user/projects/notes.txt` is entrance, then `home`, then `your-user`, then `projects`, then the file. Permissions are the keys to the rooms and the papers.

The separation is deliberate, and it is a security design, not just tidiness. User files sit apart from system files so a user mistake doesn't break the system. Settings sit apart from logs so you can check one without mixing it with the other. Temporary files sit apart from permanent ones so short-lived mess doesn't pollute real data. When you audit a system, you are really checking whether that separation is still intact, or whether someone has mixed things up, opened keys too wide, or left sensitive data in a temporary spot.

## The folders that matter

| Folder | What lives there |
|--------|------------------|
| `/` | Root, top of everything |
| `/home` | Each normal user's personal folder, like `/home/your-user` |
| `/root` | Root's home only; normal users don't go in |
| `/etc` | System-wide settings |
| `/var` | Data that changes often — logs, caches, service data |
| `/tmp` | Short-lived temporary files, often cleared on reboot |
| `/usr` | User programs, libraries, documentation |
| `/bin` | Essential commands like `ls`, `cat`, `cp` (often linked from `/usr/bin` on modern systems) |
| `/sbin` | System admin commands like `ip` |
| `/opt` | Software installed by hand, outside system packages |
| `/boot` | Files needed to start the computer — do not touch without understanding |
| `/dev` | Devices presented as files |
| `/proc` | Live process and kernel info, not real files on disk |
| `/sys` | Live hardware and kernel info, like `/proc` but for devices |
| `/mnt`, `/media` | Mount points for external drives and other filesystems |

Run `ls /` on your own machine and compare. The exact list varies by system, and that variation is normal.

**`/` — root.** Don't create random folders here. Top-level layout belongs to the OS. Practise in `/home` or a test folder. In an audit, `/` should be root-owned and not writable by everyone — if it is, the whole design is broken.

**`/home` — users' homes.** Your room in the building: projects, notes, downloads, and your own settings (hidden files starting with `.`, like `.bashrc`).

```bash
ls -l /home
ls -l ~
pwd
```

`~` means your home folder. Don't enter another user's home without permission, and don't delete one. Check your own home's permissions with `ls -ld ~` and understand what the letters mean — Section 4 below.

**`/root` — root's home.** Normal users can't normally enter it, by design. If one can read it in a lab, that is a serious permission problem to note, not something to use.

**`/etc` — the control room.** System-wide settings live here:

- `/etc/hostname` — the machine's name, plain text.
- `/etc/hosts` — local name-to-IP mappings, checked before DNS in some cases.
- `/etc/passwd` — list of users, with user ID, group ID, home folder, and shell. Modern Linux does not store passwords here in plain text.
- `/etc/shadow` — protected password hashes, root only. Do not read it, copy it, or share it. Checking that its permissions are root-only is enough for an audit; you will never be asked to read its contents in this roadmap.
- Service settings, often a folder per service under `/etc`, like `/etc/ssh/` if SSH is installed in a lab.

```bash
ls -l /etc/hostname
cat /etc/hostname
ls -l /etc/
```

Only `cat` files you understand and are allowed to read in your own lab. Before changing any settings file in a lab, back it up — copy it to a test name first, change one line at a time, and note what you changed and why. Settings are a top audit target: are lab defaults changed, are unnecessary services disabled, are settings files too open? Those are design questions, not just file checks.

**`/var` — changing data, especially logs.** `/var/log` is the history book: what happened, when, who did what, what failed. `/var/lib` often holds service data, like a database's files in a lab setup.

```bash
ls -l /var/log | head -20
```

Start with names and permissions. Your own lab logs are for learning what logs look like. Don't delete logs to "clean up" — in a real job, logs are evidence, and deleting them hides history. A system with no logs, or logs any normal user can rewrite, is hard to defend. A good lab test should also be detectable in logs.

**`/tmp` — temporary files.** Programs use it for short-lived work. Don't store important notes here; they may vanish on reboot. And don't put private data here — temporary files readable by everyone are a classic lab finding.

**`/usr`, `/bin`, `/sbin`.** `/usr/bin` holds most user commands — `python3`, `grep`, `ls`. `/bin` holds the essentials, `/sbin` the admin commands. All three should be root-owned and not writable by normal users. If a normal user can rewrite a program in `/usr/bin`, that program changes for everyone.

```bash
which python3
ls -l $(which python3)
```

`which` shows where a command lives; `$(...)` runs a command and drops its output into the surrounding command. Read the result slowly: path, permissions, owner.

**`/opt` — hand-installed software.** A lab tool installed manually might live in `/opt/toolname`. Note what you installed, when, and why. Untracked software is hard to audit later, and "what is this, who installed it, is it still needed, is it updated?" is a fair audit question for anything sitting here.

**`/boot` — boot files.** Kernel image, bootloader settings. Changing these wrongly can stop a machine from starting. Beginners don't practise here.

**`/dev`, `/proc`, `/sys` — special, virtual folders.** These aren't normal files on disk. They are views into the system, presented as files so normal file tools can read them.

- `/dev/null` discards anything written to it — useful for throwing away output: `command > /dev/null`. `/dev/sda` (name varies) is a disk presented as a file; never write to a disk device directly except on a disposable test disk in your own lab, and never on your main disk.
- `cat /proc/cpuinfo` shows CPU info, read-only. `ls /proc` shows a folder per PID — one per running process.
- `/sys` shows hardware and kernel info. Read-only exploration in a lab is fine. Don't change files in `/proc` or `/sys` to "tune" a system unless a trusted guide explains exactly what, why, and how to undo it.

The idea behind all three — everything is a file — is elegant, and it is also why permissions on them matter: read-only for normal users, write only where root genuinely needs it.

## Paths: absolute, relative, home

An absolute path starts with `/` and means the same file no matter where you are standing: `/home/your-user/projects/notes.txt`. Use absolute paths in scripts, reports, and instructions to someone else, where precision matters.

A relative path doesn't start with `/` and means "from where I am now". From `/home/your-user`, `projects/notes.txt` is `/home/your-user/projects/notes.txt`. `..` goes up one folder, `.` means here, and `cd -` goes back to the previous folder you were in.

```bash
pwd
cd ~/lab-test
pwd
cd ..
pwd
cd -
pwd
```

The failure mode here is boring and common: using a relative path while standing in a different folder than you thought, then creating or deleting the wrong file. The fix is just as boring and just as effective — run `pwd` and `ls -l` before any create, move, or delete, especially in a fresh terminal.

`~` is your home folder, and `$HOME` is a variable holding the same path. `echo $HOME` prints it. `~some-user` means that user's home, if you are allowed to see it in a lab — don't use it to peek into other people's homes.

## Permissions, in full

This is the section to get fluent in, not just to read. Break every permission string into owner, group, and others until it is automatic.

Run this on your own machine:

```bash
ls -l ~/lab-test/test.txt
```

Example output (yours will differ):

```
-rw-r--r-- 1 your-user your-user 38 Nov  1 12:00 /home/your-user/lab-test/test.txt
```

Left to right: `-` is the type (regular file; `d` is a directory, `l` is a link), then `rw-` is owner, `r--` is group, `r--` is others, then link count, owner user, owner group, size in bytes, last change time, and name.

So `-rw-r--r--` means: a file, owner can read and write, group and others can read only. No `x` for anyone, which is right for a text file — it isn't a program.

For a folder:

```
drwxrwxr-x 2 your-user your-user 4096 Nov  1 12:00 lab-test
```

`d` is a directory, owner rwx, group rwx, others r-x. That is `775` in numbers, below.

### What r, w, x mean — and why folders differ

| Letter | For a file | For a folder |
|--------|------------|--------------|
| r | Read the content | List names inside |
| w | Change the content | Create, delete, rename files inside |
| x | Run it as a program | Enter the folder and reach files inside |

Two nuances catch almost everyone. First, for a folder you need `x` to enter it, even if you have `r`. Without `x`, you can't reach files inside even if you know their names. Second, deleting a file depends on write permission on the folder, not just on the file. You can delete a file you can't write to, if the folder lets you. Test both in a lab with a test folder — the experiment teaches it faster than any table.

### Numbers: how the octal actually works

r=4, w=2, x=1, and `-`=0. Add per group:

- `rwx` = 7, `rw-` = 6, `r-x` = 5, `r--` = 4, `---` = 0

Combine the three groups: `rwxrwxr-x` is 7, 7, 5 — **775**. `rw-r--r--` is **644**. `rw-------` is **600**. `rwxr-xr-x` is **755**.

What the common numbers mean in practice:

| Number | Meaning | Good for | Watch out |
|--------|---------|----------|-----------|
| 600 | Owner read/write only | Private file, personal test notes in a lab | A service that needs to read it will fail — fix the design, don't just open it up |
| 644 | Owner read/write, everyone else read | Ordinary public text file, README | Too open if the file holds anything private |
| 755 | Owner full, everyone else read and enter | Program or folder everyone should use but only the owner changes | Folder 755 doesn't protect the files inside — check those too |
| 775 | Owner and group full, others read and enter | Shared project folder for a controlled group in a lab | Too open if the group is large or includes people who shouldn't change files |
| 777 | Everyone full | Almost never. A disposable test folder, for minutes, with test data only, changed back after | Almost always a finding: anyone can change or delete |

Watch out for the classic shortcut: a program says "permission denied" and someone runs `chmod 777` to make it go away. That fixes the symptom by opening the file to everyone. The real fix, in your own lab, is to check who owns the file (`ls -l`), check who you are (`whoami`, `groups`), work out the one permission you actually need — read, write, or enter — and change only that, for only that group, to the minimum that works. Then test again. If it still fails, check the folder permissions too: you need `x` to enter a folder and `w` on it to create or delete inside.

### Changing permissions safely

Test files only, own machine only:

```bash
mkdir -p ~/lab-test
echo "test" > ~/lab-test/a.txt
ls -l ~/lab-test/a.txt
chmod 600 ~/lab-test/a.txt
ls -l ~/lab-test/a.txt
chmod 644 ~/lab-test/a.txt
ls -l ~/lab-test/a.txt
```

Always `ls -l` before and after. That before-and-after habit is the audit habit.

Symbolic form is also worth knowing: `chmod u+x file` adds execute for the owner, `chmod g-w file` removes write for the group, `chmod o-r file` removes read for others. `u` is owner, `g` is group, `o` is others, `a` is all.

`chown` changes who owns a file. Normally only root can hand ownership to another user, and doing it wrong can lock the original owner out. Understand the concept — owner matters because owner permissions apply to the owner — but don't practise `chown` on system files.

`umask` sets the defaults for new files. Roughly, it subtracts from the maximum: files start at 666, folders at 777, and the umask removes some. With a umask of `022`, new files come out 644 and new folders 755. With `077`, new files are 600 and folders 700 — the private-work default. Check yours with `umask` in a lab, create a test file, and see the effect with `ls -l`. Choose by design, private versus shared, not by habit.

### Why ownership matters for red teaming

A root-owned file at 644 can be read by everyone but changed only by root — sound design for a system file everyone needs to read. A service-owned file at 600 can be touched only by that service — sound design for private service data. A group-owned folder at 775 lets a controlled group work together while others only look in. The audit question is always the same: is this file owned by the right user, in the right group, at the minimum permission its purpose needs? A private file at 644, or a system file writable by everyone, is a finding, with a fix: tighten to the minimum and ask why it was open in the first place.

## Auditing a filesystem, the way it actually goes

This is what the System Audit project does in code — system info, permission checks with `os.stat`, localhost ports, a saved report. The manual version, on your own project folder only, goes like this:

Identify where you are and who you are — `pwd`, `whoami`, `ls -l`. Don't audit a folder you don't own or a system folder you don't understand yet without a guide.

List the folder itself with `ls -ld <folder>` and its contents with `ls -l <folder>`. Note type, owner, group, permission number, and size for each item.

Work through permissions one by one and ask the three design questions each time: who needs to read this, who needs to change this, who should not? Anything wider than needed goes in the report as a lab finding with a fix idea — for example, a test file holding private test notes at 644 becomes `chmod 600`, because only the owner needs it.

Check ownership: is the owner and group right for the file's purpose? A system file owned by a normal user in a lab is unusual and worth asking about.

Then check the risk patterns: anything at 777, any private file at 644 or wider, any folder writable by others that shouldn't be, any file you don't recognise. Don't delete the unknown file on sight — note it, learn what it is from documentation, then decide.

Write it up as a short report in your lab folder — date, folder audited, findings, fix ideas — and strip private details before sharing or publishing anything. Then explain the report out loud in a few minutes without reading it word for word: what you audited, what you found, what the risk is in lab terms, and what the fix is. If you can't explain a finding, you haven't understood it yet.

A few lab habits worth keeping: practise deletes only in `~/lab-test` with files you just listed, never `rm -rf /` or `rm -rf ~`, and never store important work in `/tmp`. `/tmp` is temporary by design; important work lives in `/home`, backed up or pushed to Git for projects.

## A quick lab pass

Work through this once, slowly, on your own machine, and most of the file above stops being theory:

```bash
ls /
ls -l /home
ls -ld ~
ls -l /etc/hostname
cat /etc/hostname
ls -l /var/log | head -20
mkdir -p ~/lab-test/perm
echo "hello lab" > ~/lab-test/perm/a.txt
ls -l ~/lab-test/perm/a.txt
chmod 600 ~/lab-test/perm/a.txt
ls -l ~/lab-test/perm/a.txt
chmod 700 ~/lab-test/perm
ls -ld ~/lab-test/perm
```

For each step, say out loud what the number means, what the letters mean, and what it allows for owner, group, and others. Explain why 600 is private, why 644 is public-readable, and why 700 on a folder is owner-only. Then try the folder experiment from earlier: set a test folder to 444, try to `cat` a file inside it, note the error, set it to 555, and try again. That one experiment settles the difference between folder read and folder execute better than memorising the table.

Hidden files are worth a mention too: a name starting with `.` is just hidden from a plain `ls` listing, not secret and not protected. Your shell settings live in dotfiles in your home folder, and `ls -a` is how you see them. Hidden means "don't clutter the listing", nothing more.

When you can draw the tree from `/` down with ten or more folders and explain each one, read any `ls -l` line without hesitation, convert letters to numbers and back, explain folder `x` versus folder `r` and why folder `w` controls deletion, and audit your own project folder and explain the report line by line, move on to `linux-commands.md`.

---

*Lab only: systems you own or have written permission to test, test data only. Permissions check only for private files — never read shadow data, private keys, or other users' private files.*
