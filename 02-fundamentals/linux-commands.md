# 02 — Fundamentals: Linux Commands, Fully Explained

## What this file is

This is Phase 1, part 3. You already know what a computer is, and you already know the filesystem. Now you learn how to *move* in that filesystem, *read* files, *find* things, *change* permissions safely, and *combine* commands — all on your own machine only, with test files you made yourself.

No shortcuts here. Every command gets the same big structure:
- **What it is, in simple words**
- **Why it is used**
- **When to use it, and when NOT to use it**
- **Lab example, with test data only**
- **Output meaning, line by line**
- **Common mistakes**

If you learn commands this way — purpose first, example second, output meaning third — you will not forget them tomorrow. If you only memorise names, you will forget them in a week.

**Safety rule for this whole file:**
All examples use `~/lab-test` and test files you create yourself, on your own Kali machine only.
- Never run a delete, move, or permission change on a real system folder, on another user's files, or on files you do not understand yet.
- Before any delete or move, always run `pwd` and `ls -l` first, and check you are in the test folder you intended.
- Never read private files, shadow passwords, or private keys. Permissions check only is enough for an audit.

---

## 0. How to use a command — the pattern behind every command

Almost every Linux command follows the same pattern:

```
command [options] [arguments]
```

- **command:** What to do. Example: `ls` = list, `cat` = show file content.
- **options:** How to do it, usually starting with `-`. Example: `ls -l` = list in long form, with permissions. `ls -a` = list all, including hidden files starting with `.`.
- **arguments:** What to do it *to*. Example: `ls /home` = list the `/home` folder. `cat notes.txt` = show the file `notes.txt`.

**How to learn any new command, in 30 seconds, in lab:**
```bash
command --help
man command
```
- `--help` = short help, usually enough to start.
- `man` = manual, full documentation. Press `q` to quit `man`.
- Example: `ls --help`, `man ls`, `grep --help`.

**Practical design note:**
Professionals do not memorise every option. They remember the *purpose* of a command, and they check `--help` for the exact option when they need it. That is a design habit: purpose in memory, details in documentation. Do not try to memorise 50 options for `ls`. Memorise: "`ls` lists, `-l` gives details, `-a` shows hidden". That is enough to start, and `--help` gives the rest when you need it.

---

## 1. Where am I, and what is here? — `pwd`, `ls`, `cd`

These three are your navigation system. You will use them every day, in every lab, before any other command.

### 1.1 `pwd` — Print Working Directory

- **What:** Shows the full path of the folder you are in right now.
- **Why:** So you never create, move, or delete a file in the wrong folder by mistake.
- **When:** Before any create, move, delete, or permission change. Always.
- **Lab example (own machine only):**
  ```bash
  pwd
  ```
  Example output (yours will differ by username and folder):
  ```
  /home/your-user
  ```
  Meaning: You are in `/home/your-user`. That is your home folder.
- **Common mistake:** Skipping `pwd` and assuming you are in the right folder, then deleting the wrong file. Fix: `pwd` first, every time, especially in a new terminal.

### 1.2 `ls` — List

- **What:** Lists files and folders in a folder.
- **Why:** To see what exists, before you open, move, or delete anything.
- **When:** After `cd`, before any file work, and after any create/move/delete to verify what changed.
- **Lab examples:**
  ```bash
  ls
  ls -l
  ls -a
  ls -l ~/lab-test
  ls -ld ~/lab-test
  ```
  Meaning, option by option:
  - `ls` alone = list names only, in current folder, short form.
  - `ls -l` = long form: permissions, owner, group, size, time, name, one per line. This is the audit form — always use `-l` when checking permissions.
  - `ls -a` = all, including hidden files starting with `.`, like `.bashrc`. Hidden does not mean secret — it means "settings file, do not clutter normal listing".
  - `ls -l ~/lab-test` = long list of that specific folder, even if you are not in it.
  - `ls -ld ~/lab-test` = details of the folder *itself*, not files inside it. `d` = directory itself. Use this to check a folder's own permissions, not its contents.
- **Output meaning — reading one `ls -l` line (full detail is in `linux-filesystem.md`):**
  ```
  -rw-r--r-- 1 your-user your-user 38 Oct 31 12:00 test.txt
  ```
  - `-` = regular file (not folder).
  - `rw-` = owner: read + write.
  - `r--` = group: read only.
  - `r--` = others: read only.
  - `your-user your-user` = owner user, owner group.
  - `38` = size in bytes.
  - `test.txt` = name.
- **Common mistakes:**
  - Using `ls` alone to check permissions. Fix: use `ls -l` for permissions, always.
  - Using `ls -l folder` when you wanted folder's own permissions, and getting confused by contents list. Fix: use `ls -ld folder` for the folder itself.

### 1.3 `cd` — Change Directory

- **What:** Moves you into a different folder.
- **Why:** So you can work in the right place, with short relative paths, instead of typing full paths every time.
- **When:** At the start of a lab task, to go to your project or test folder.
- **Lab examples (own machine only):**
  ```bash
  cd ~/lab-test
  pwd
  cd ..
  pwd
  cd -
  pwd
  cd ~
  pwd
  cd
  pwd
  ```
  Meaning:
  - `cd ~/lab-test` = go to lab-test folder in your home. `~` = home.
  - `cd ..` = go up one folder. From `/home/your-user/lab-test`, `..` goes to `/home/your-user`.
  - `cd -` = go back to previous folder you were in. Useful when you jump between two folders.
  - `cd ~` = go to home folder.
  - `cd` alone (no argument) = also go to home folder, same as `cd ~`.
- **Common mistakes:**
  - Typing `cd` with a wrong capital letter: Linux is case-sensitive. `Lab-Test` and `lab-test` are different folders. Fix: use `ls` to see exact spelling, then `cd` with that exact spelling.
  - Using `cd` into a folder that does not exist, and then running other commands in the old folder by mistake. Fix: after `cd`, always run `pwd` to confirm you actually moved. If `cd` prints "no such file or directory", you stayed where you were — that is why your earlier `system_audit.py` run failed: `cd system-audit` failed, so Python looked in the old folder.

---

## 2. Reading files — `cat`, `less`, `head`, `tail`

### 2.1 `cat` — Show file content

- **What:** Prints a whole text file to the terminal.
- **Why:** To read small text files: notes, settings you understand, test files you made.
- **When:** For small files only. For large files, use `less` or `head`/`tail` below, so output does not flood your screen.
- **Lab example (test file only):**
  ```bash
  mkdir -p ~/lab-test
  echo "Hello lab, this is a test line." > ~/lab-test/a.txt
  cat ~/lab-test/a.txt
  ```
  Meaning:
  - `echo "text" > file` = write text into file, creating it if needed, replacing old content if file already existed. `>` = redirect output into a file. Be careful: `>` replaces, it does not add. See Section 8 for `>` vs `>>`.
  - `cat` output:
    ```
    Hello lab, this is a test line.
    ```
  - That is the file content, exactly as written.
- **Common mistakes:**
  - Using `cat` on a large log file and flooding the terminal. Fix: use `head` or `less` for large files.
  - Using `cat` on a binary file and getting unreadable output. Fix: binary files are not text — do not `cat` them to guess meaning. Use the right tool for that file type.
  - Using `cat` on private files you should not read. Fix: permissions check only for private files, as in filesystem file.

### 2.2 `less` — Read a file page by page

- **What:** Opens a file in a viewer where you can scroll up/down, search, and quit.
- **Why:** For large files, like logs, where `cat` would flood the screen.
- **When:** Reading logs in your own lab, reading long documentation files.
- **Lab example:**
  ```bash
  less ~/lab-test/a.txt
  ```
  Inside `less`:
  - `Space` or `PageDown` = next page.
  - `b` = previous page.
  - `/word` then Enter = search for `word` forward.
  - `q` = quit and return to terminal.
- **Common mistake:** Forgetting how to quit `less` and closing the terminal instead. Fix: remember `q` = quit, for `less` and for `man`.

### 2.3 `head` and `tail` — First lines and last lines

- **What:** `head` shows first lines of a file. `tail` shows last lines.
- **Why:** Logs grow large. Usually you care about the most recent lines (end of log) or the first lines (start of a file).
- **When:** Checking a log file in your own lab, checking a large output file quickly.
- **Lab examples:**
  ```bash
  head -20 ~/lab-test/a.txt
  tail -20 ~/lab-test/a.txt
  tail -f ~/lab-test/a.txt
  ```
  Meaning:
  - `head -20` = first 20 lines. Change 20 to any number you want.
  - `tail -20` = last 20 lines.
  - `tail -f` = follow: keep showing new lines as file grows, live. Press `Ctrl+C` to stop following. Useful in lab when a program writes to a log live, but do not leave it running forever.
- **Common mistake:** Using `cat` for a 10,000-line log, then scrolling forever. Fix: `head`/`tail` first, then `less` if you need to search inside.

---

## 3. Creating, copying, moving, deleting — `mkdir`, `touch`, `cp`, `mv`, `rm`

These are powerful. Powerful means: check `pwd` and `ls -l` first, every time, especially for `mv` and `rm`.

### 3.1 `mkdir` — Make Directory

- **What:** Creates a new folder.
- **Why:** To organise projects, notes, lab test folders.
- **When:** At the start of a project or lab task.
- **Lab examples:**
  ```bash
  mkdir ~/lab-test/myfolder
  mkdir -p ~/lab-test/a/b/c
  ```
  Meaning:
  - First command creates one folder, if parent exists.
  - `-p` = parents: create parent folders too if they do not exist, and do not error if folder already exists. `mkdir -p ~/lab-test/a/b/c` creates `a`, then `b` inside it, then `c` inside `b`, in one command. This is the safe, common way to create nested folders in lab.
- **Common mistake:** Forgetting `-p` for nested folders and getting "no such file or directory" because parent does not exist yet. Fix: use `-p` for nested paths.

### 3.2 `touch` — Create empty file, or update time

- **What:** Creates an empty file if it does not exist, or updates its time if it already exists (without changing content).
- **Why:** To create a placeholder file quickly, like `notes.txt` at project start, before you write in it.
- **When:** Project setup, test file creation.
- **Lab example:**
  ```bash
  touch ~/lab-test/notes.txt
  ls -l ~/lab-test/notes.txt
  ```
  Output will show size 0 for a new empty file. That is normal — empty means 0 bytes, not broken.
- **Common mistake:** Thinking `touch` writes content. Fix: `touch` only creates empty file or updates time. To write content, use an editor like `nano`, or `echo > file` for a simple line (see Section 8).

### 3.3 `cp` — Copy

- **What:** Copies a file or folder to a new location. Original stays where it was.
- **Why:** Backup before changing a settings file in lab, copy a test file to try a change without losing original.
- **When:** Before any risky change in lab: copy first, change the copy, keep original safe.
- **Lab examples:**
  ```bash
  cp ~/lab-test/a.txt ~/lab-test/a-backup.txt
  cp -r ~/lab-test/myfolder ~/lab-test/myfolder-copy
  ```
  Meaning:
  - First: copy file `a.txt` to `a-backup.txt`, in same folder, new name. Original `a.txt` still exists.
  - Second: copy a folder. `-r` = recursive: copy folder and everything inside it, including subfolders. Without `-r`, `cp` will not copy a folder, it will error — that is a safety feature, not a bug.
- **Common mistakes:**
  - Forgetting `-r` for folders. Fix: `cp -r` for folders, `cp` alone for files.
  - Copying over an existing file without checking, and losing the old version. Fix: `ls -l` destination first, and use a backup name like `file-backup.txt`, not the same name, when you mean to keep both.

### 3.4 `mv` — Move / Rename

- **What:** Moves a file/folder to a new location, OR renames it if new location is same folder with new name. Original location no longer has it after move — unlike `cp`.
- **Why:** To organise files, rename a file to a better name, move a finished report to a reports folder.
- **When:** Organising, renaming, moving a file you no longer need in current folder.
- **Lab examples:**
  ```bash
  mv ~/lab-test/a.txt ~/lab-test/b.txt
  mv ~/lab-test/b.txt ~/lab-test/myfolder/
  ```
  Meaning:
  - First: rename `a.txt` to `b.txt`, same folder. After this, `a.txt` no longer exists, `b.txt` does.
  - Second: move `b.txt` into `myfolder` folder, keeping same name inside that folder. After this, `b.txt` is no longer in lab-test root, it is in `myfolder/b.txt`.
- **Common mistakes:**
  - Thinking `mv` copies like `cp`. Fix: `mv` moves — original disappears from old place. If you want both places, use `cp` first, then `mv` the copy if needed, or just `cp`.
  - Moving a file to the wrong folder because you did not check `pwd` first, then not finding it later. Fix: `pwd`, `ls -l` before, `ls -l` destination after, to verify move worked as intended.

### 3.5 `rm` — Remove / Delete — the most careful command in this file

- **What:** Deletes a file or folder, permanently (not to a trash bin, in terminal). Deleted in terminal usually cannot be undone easily.
- **Why:** To clean test files, remove a folder you created for a test and no longer need.
- **When:** Only in test folders you created yourself, after checking `pwd` and `ls -l`, and only for files you are sure you do not need, or have backed up.
- **Lab examples (test files only, own machine only):**
  ```bash
  mkdir -p ~/lab-test/delete-me
  touch ~/lab-test/delete-me/temp.txt
  ls -l ~/lab-test/delete-me/
  rm ~/lab-test/delete-me/temp.txt
  ls -l ~/lab-test/delete-me/
  rm -r ~/lab-test/delete-me
  ls -l ~/lab-test/
  ```
  Meaning, step by step:
  - Create test folder and test file, list to confirm they exist.
  - `rm file` deletes that one file. After, listing shows folder empty.
  - `rm -r folder` deletes folder and everything inside it, recursively. `-r` is required for folders, like `cp -r`.
  - Final `ls` confirms folder is gone.
- **Dangerous forms — NEVER use these unless you fully understand and are in a disposable test VM, with a backup:**
  - `rm -rf /` — would try to delete everything from root. Never type this, even as a joke.
  - `rm -rf ~` — would try to delete your home folder. Never type this.
  - `rm -rf *` in the wrong folder — would delete everything in current folder. That is why `pwd` first is a rule, not a suggestion.
  - `rm -rf` with a variable you did not check, like `rm -rf $FOLDER` where `$FOLDER` is empty — could delete wrong place. Check variable first with `echo $FOLDER`.
- **Safe delete algorithm (use every time):**
  ```
  1. pwd — where am I?
  2. ls -l — what is here, exactly?
  3. Is this a test file/folder I made, and do I have a backup if needed?
  4. If yes to all, then rm that exact name, with full path if possible, like rm ~/lab-test/delete-me/temp.txt
  5. ls -l again — verify it is gone, and nothing else is gone.
  ```
- **Common mistake:** Deleting with a wildcard `*` without listing first. Example: `rm *.txt` in the wrong folder deletes all `.txt` files there. Fix: run `ls *.txt` first to see exactly what matches, then `rm` only if that list is exactly what you intend to delete.

---

## 4. Searching inside files and finding files — `grep`, `find`, `which`, `history`

### 4.1 `grep` — Search text inside files

- **What:** Searches for a word or pattern inside files, and prints lines that match.
- **Why:** To find a setting, an error, a name, inside a large file or many files, without reading the whole file.
- **When:** Checking logs in your own lab, finding a word in notes, checking a settings file for a specific line.
- **Lab examples (test files only):**
  ```bash
  echo "hello lab" > ~/lab-test/search.txt
  echo "second line with password test" >> ~/lab-test/search.txt
  echo "third line" >> ~/lab-test/search.txt
  grep "lab" ~/lab-test/search.txt
  grep -i "LAB" ~/lab-test/search.txt
  grep -r "hello" ~/lab-test/
  ```
  Meaning:
  - First three `echo` commands create a test file with three lines. `>>` adds (appends) to file, without replacing old content — see Section 8.
  - `grep "lab" file` prints only lines containing `lab` (case-sensitive: `lab` matches, `LAB` does not, unless `-i` is used).
  - `grep -i "LAB"` = case-insensitive: matches `lab`, `LAB`, `Lab`, etc. `-i` = ignore case.
  - `grep -r "hello" folder/` = recursive: search inside all files in folder and subfolders. `-r` = recursive, like `cp -r`.
  - Output for first grep would be:
    ```
    hello lab
    ```
    Only the matching line, not the whole file. That is the point of `grep`: filter to what matters.
- **Common mistakes:**
  - Forgetting quotes around pattern with spaces. Fix: always quote pattern, like `grep "hello lab" file`, not `grep hello lab file` (second form would be interpreted as two separate arguments).
  - Using `grep` on binary files and getting unreadable output. Fix: `grep` is for text files. For binary, use the right tool for that file type.

### 4.2 `find` — Find files by name, type, time, etc.

- **What:** Searches a folder tree for files matching criteria: name, type, size, time, etc.
- **Why:** To locate a file when you forgot where you put it, or to list all files of a type in a project.
- **When:** Project organisation, audit (list all `.txt` files, list all files changed recently in lab, etc.), always in your own lab folders first.
- **Lab examples:**
  ```bash
  find ~/lab-test -name "*.txt"
  find ~/lab-test -type f
  find ~/lab-test -type d
  ```
  Meaning:
  - First: find files in lab-test (and subfolders) whose name ends with `.txt`. `*.txt` in quotes means "any name ending with .txt". Quotes prevent shell from expanding `*` before `find` sees it — important detail, remember quotes for `find -name`.
  - Second: `-type f` = files only, not folders.
  - Third: `-type d` = directories (folders) only.
- **Common mistakes:**
  - Running `find /` (whole system) and waiting forever, with huge output. Fix: always start `find` in a specific folder you own, like `~/lab-test` or your project folder, not `/`, unless a guide tells you to and you understand why.
  - Forgetting quotes for `*.txt`, so shell expands it in current folder before `find` runs, giving wrong results. Fix: quote patterns for `find -name`.

### 4.3 `which` — Where is this command?

- **What:** Shows the full path of a command, like where `python3` lives on disk.
- **Why:** To verify which version of a tool you are actually running, and to understand filesystem layout (commands live in `/usr/bin`, etc.).
- **When:** When a command behaves unexpectedly, or when learning where tools live.
- **Lab example:**
  ```bash
  which python3
  which ls
  ls -l $(which python3)
  ```
  Meaning: First two print paths, like `/usr/bin/python3`. Third lists details of that file, so you see permissions and owner.
- **Common mistake:** Thinking `which` shows what a command does. Fix: `which` shows where, `--help` and `man` show what and how.

### 4.4 `history` — What commands did I run before?

- **What:** Lists commands you ran before in this terminal session history.
- **Why:** To recall a long command you ran yesterday, to repeat it, or to check what you did in a lab session for your notes.
- **When:** Writing notes after a lab, repeating a successful command, debugging ("what did I run just before the error?").
- **Lab example:**
  ```bash
  history | tail -20
  ```
  Meaning: Show last 20 commands from history. `|` sends history output into `tail`, which shows last lines — see Section 7 for pipes.
- **Common mistake:** Copying a history command blindly without checking folder and arguments first. Fix: read the history line fully, check `pwd` and arguments, then run it again if it is still correct for current folder.

---

## 5. Permissions commands — `chmod`, `chown` concept, `ls -l` again

Full permission theory is in `linux-filesystem.md`. Here is the command practice, with lab examples only.

### 5.1 `chmod` — Change permissions

- **What:** Changes read/write/execute permissions for owner/group/others.
- **Why:** To make a private file private, a shared folder shared, or a script executable in your own lab.
- **When:** After creating a file, when audit shows permissions too open or too closed for its purpose, in your own lab only.
- **Lab examples (test files only):**
  ```bash
  echo "test" > ~/lab-test/perm.txt
  ls -l ~/lab-test/perm.txt
  chmod 600 ~/lab-test/perm.txt
  ls -l ~/lab-test/perm.txt
  chmod 644 ~/lab-test/perm.txt
  ls -l ~/lab-test/perm.txt
  chmod u+x ~/lab-test/perm.txt
  ls -l ~/lab-test/perm.txt
  ```
  Meaning:
  - First `ls -l` shows original, often 644.
  - `chmod 600` = owner read/write only, group/others none. Private design.
  - `chmod 644` = back to owner read/write, others read. Public-readable design for a normal text file.
  - `chmod u+x` = symbolic form: add execute (`+x`) for user/owner (`u`). After this, owner permissions become `rwx` (7), so file becomes `744` (owner rwx, group r, others r). For a text file, `x` is usually not needed — this example is just to show symbolic form works. In real lab, do not add `x` to text files without reason.
  - Always `ls -l` before and after `chmod`, to see the change. That is the audit habit.
- **Common mistakes:**
  - Using `chmod 777` to fix "permission denied". Fix: use minimum-permission design from filesystem file: check owner, check who you are, check what you actually need, change only that.
  - Changing permissions on system files without understanding. Fix: practise `chmod` only on test files you made, in `~/lab-test`, until permission logic is automatic.

### 5.2 `chown` — Change owner (concept, careful)

- **What:** Changes who owns a file or folder.
- **Why:** When a file should belong to a different user or service account in lab design.
- **When:** Rarely for beginners. Understand concept first: owner matters, because owner permissions apply to owner, group permissions to group members, others to everyone else.
- **Lab note:** Changing owner usually requires root, and changing owner wrongly can lock the original owner out. Do not practise `chown` on system files. If a course guides you in a disposable lab VM, follow that guide exactly, with a backup, and only for test files.
- **Common mistake:** Thinking `chmod` changes owner. Fix: `chmod` = change permissions (mode). `chown` = change owner. Different commands, different jobs.

### 5.3 `whoami`, `id`, `groups` — Who am I?

- **What:**
  - `whoami` = prints your current username.
  - `id` = prints user ID, group ID, and groups you belong to, in numbers and names.
  - `groups` = prints groups you belong to, names only.
- **Why:** Permissions depend on who you are and which groups you are in. Before a "permission denied" debug, check who you are first.
- **Lab examples:**
  ```bash
  whoami
  id
  groups
  ```
  Output meaning: `whoami` is one word, your username. `id` shows `uid` (user ID), `gid` (group ID), `groups` list. If a file is owned by group `your-user` and you are in group `your-user`, group permissions apply to you. If not, others permissions apply.
- **Common mistake:** Debugging permissions without checking `whoami` first, and assuming you are root or another user. Fix: `whoami` first, then `ls -l` file, then compare owner/group to you.

---

## 6. System info and processes — `uname`, `hostname`, `ps`, `ss`, `top` concept

### 6.1 `uname`, `hostname`

- **What:**
  - `uname -a` = system info: kernel name, version, machine type, etc., all in one line.
  - `hostname` = computer name.
- **Why:** For system audit reports, for notes (which machine did this lab on?), and for understanding OS version in lab.
- **Lab examples:**
  ```bash
  uname -a
  hostname
  ```
  Output meaning: `uname -a` line includes Linux, kernel version, architecture like `x86_64`, etc. Do not memorise kernel version numbers — note them in audit report, and understand: newer kernel usually means newer features and patches, but version alone does not prove security.

### 6.2 `ps` — Processes

- **What:** Lists running processes.
- **Why:** To see what is running on your own machine right now.
- **When:** System audit, debugging "why is my computer slow in lab?", learning what services run by default.
- **Lab examples (own machine only):**
  ```bash
  ps aux | head -20
  ps aux | grep python3
  ```
  Meaning:
  - First: all processes, first 20 lines only, so readable.
  - Second: filter processes to only lines containing `python3`. Note: `grep python3` itself will also appear as a process in output, because grep is running while ps runs — that is normal, ignore the grep line itself when reading.
  - Columns: USER (owner), PID (process ID number), %CPU, %MEM, COMMAND (what started it).
- **Common mistake:** Killing a process by PID without learning what it is first. Fix: note PID and COMMAND, learn from docs, then decide. In lab, do not kill system processes blindly.

### 6.3 `ss` — Sockets, listening ports (own machine only)

- **What:** Shows network sockets, especially listening ports.
- **Why:** System audit: what services are waiting for connections on your own machine?
- **When:** Every system audit, lab setup check.
- **Lab example:**
  ```bash
  ss -tln
  ```
  Meaning, as in computer-basics file:
  - `-t` = TCP, `-l` = listening, `-n` = numbers not names.
  - `127.0.0.1:5432` = localhost only, safer by design.
  - `[::1]:5432` = IPv6 localhost only, same idea.
  - `0.0.0.0:5432` = all IPv4 networks, check carefully in lab design.
- **Common mistake:** Thinking an open port alone means vulnerable. Fix: open port = service present. Next questions: what service, what version, should it be open, to whom, is it patched?

### 6.4 `top` / `htop` concept — Live process viewer

- **What:** `top` shows processes live, updating, sorted by CPU or memory use. `htop` is a friendlier version, if installed in lab.
- **Why:** To see, live, which process uses most CPU/memory in your own lab.
- **When:** When lab feels slow, or to learn what runs normally on your machine.
- **How to use (lab only):**
  ```bash
  top
  ```
  Inside `top`, press `q` to quit. Do not press other keys randomly in `top` until you learn them from `man top` or a guide — some keys can kill processes in `top`, and you do not want to kill by accident in lab.
- **Common mistake:** Leaving `top` running and thinking terminal is frozen. Fix: `q` quits `top` and returns to normal terminal.

---

## 7. Combining commands — pipes `|`, `grep` as filter, `&&` concept

This section is where Linux becomes powerful. Single commands are tools. Combined commands are a workflow.

### 7.1 Pipe `|` — Send output of one command into next

- **What:** `command1 | command2` means: take output of command1, use it as input to command2.
- **Why:** To filter, count, sort, or search output, without saving to a file first.
- **Lab examples:**
  ```bash
  ls -l ~/lab-test | grep ".txt"
  ps aux | grep python3
  ps aux | head -20
  cat ~/lab-test/search.txt | grep "lab"
  ```
  Meaning:
  - First: list lab-test in long form, then filter only lines containing `.txt`. Useful when a folder has many files.
  - Second: list processes, filter only python3 lines (plus grep line itself, as noted).
  - Third: list processes, show first 20 lines only.
  - Fourth: show search.txt content, filter only lines with `lab`.
- **Practical design note:** Pipes are a design pattern: small tools, each doing one job well, combined to do a bigger job. That is Unix design philosophy, and it is also good red-team design: one tool to list, one tool to filter, one tool to count, combined, instead of one huge tool that does everything badly.

### 7.2 `wc` — Count lines, words, characters

- **What:** Counts lines (`-l`), words (`-w`), characters/bytes (`-c`) in input.
- **Why:** To count how many matching lines `grep` found, how many files in a list, how many lines in a log in lab.
- **Lab examples:**
  ```bash
  grep "lab" ~/lab-test/search.txt | wc -l
  ls ~/lab-test | wc -l
  ```
  Meaning:
  - First: count lines containing `lab` in search.txt. Output is a number, like `1`.
  - Second: count files/folders listed in lab-test (names only, not recursive). Useful for quick counts in lab.

### 7.3 `sort`, `uniq` concept — Sort and remove duplicates

- **What:** `sort` sorts lines alphabetically. `uniq` removes duplicate adjacent lines (so usually use `sort` first, then `uniq`).
- **Why:** To organise output, find unique values in a log in lab, count unique IPs in a lab log (lab only, own logs).
- **Lab example:**
  ```bash
  sort ~/lab-test/search.txt
  sort ~/lab-test/search.txt | uniq
  ```
  Meaning: First sorts lines. Second sorts, then removes duplicates, so each unique line appears once. For counting unique lines, combine with `wc -l`: `sort file | uniq | wc -l`.

### 7.4 `&&` and `;` — Run commands in sequence

- **What:**
  - `command1 && command2` = run command2 only if command1 succeeded (exit status 0, no error).
  - `command1 ; command2` = run command2 after command1, regardless of success or failure.
- **Why:** To chain steps safely: only move to next step if previous step worked.
- **Lab examples:**
  ```bash
  mkdir -p ~/lab-test/chain && cd ~/lab-test/chain && pwd
  cd ~/lab-test ; ls -l
  ```
  Meaning:
  - First: create folder, and only if creation succeeded, go into it and print where you are. If mkdir fails, cd will not run, so you will not end up in the wrong folder by mistake. This is safer design than `;` for dependent steps.
  - Second: go to lab-test, then list, even if cd failed (then ls will list old folder — be careful, check output). Use `&&` for dependent steps, `;` only when steps are independent.
- **Common mistake:** Using `;` for dependent steps, then running a delete or move in the wrong folder because previous `cd` failed silently. Fix: use `&&` for dependent steps, and `pwd` to verify after `cd`.

---

## 8. Redirection — `>`, `>>`, `<` concept, and `echo`

### 8.1 `>` — Redirect output into a file (replace)

- **What:** Sends command output into a file, replacing old content if file already existed, creating file if not.
- **Lab examples:**
  ```bash
  echo "first line" > ~/lab-test/out.txt
  cat ~/lab-test/out.txt
  echo "second line" > ~/lab-test/out.txt
  cat ~/lab-test/out.txt
  ```
  Meaning:
  - After first echo, out.txt contains "first line".
  - After second echo with `>`, out.txt contains only "second line" — first line is gone, replaced. That is the danger and the use of `>`: replace, not add.
- **Common mistake:** Using `>` when you meant to add, and losing old content. Fix: use `>>` to add, see below, and back up important files before redirecting into them.

### 8.2 `>>` — Append (add to end, do not replace)

- **What:** Sends command output into a file, adding to the end, keeping old content.
- **Lab examples:**
  ```bash
  echo "first line" > ~/lab-test/out2.txt
  echo "second line" >> ~/lab-test/out2.txt
  echo "third line" >> ~/lab-test/out2.txt
  cat ~/lab-test/out2.txt
  ```
  Output:
  ```
  first line
  second line
  third line
  ```
  All three lines preserved, in order. That is append design.
- **Practical use:** Building a report file line by line in a script, adding log lines, collecting outputs from multiple commands into one report file in lab.

### 8.3 `<` — Input from a file (concept)

- **What:** Sends a file as input to a command, instead of typing input manually.
- **Example concept:**
  ```bash
  grep "lab" < ~/lab-test/search.txt
  ```
  Meaning: grep reads search.txt as input, instead of you typing lines manually. In practice, most commands accept a filename argument directly (`grep "lab" file`), so `<` is less common for beginners, but understand the idea: input can come from a file, not just from keyboard.
- **Common mistake:** Confusing `>` (output to file) with `<` (input from file) direction. Fix: think of arrow direction: `>` points to where output goes (file). `<` points from where input comes (file). Arrow direction = data flow direction.

### 8.4 `echo` — Print text

- **What:** Prints text to terminal, or, with redirection, into a file.
- **Why:** Quick test output, creating simple test files, debugging scripts (print a variable, print a step).
- **Lab examples:**
  ```bash
  echo "Hello lab"
  echo $HOME
  echo "Test line" > ~/lab-test/echo-test.txt
  cat ~/lab-test/echo-test.txt
  ```
  Meaning:
  - First prints Hello lab.
  - Second prints your home path, because `$HOME` is a variable holding home path. `echo` without quotes still expands variables — remember that when you want literal `$HOME` text, use single quotes: `echo '$HOME'` prints `$HOME` literally, not the path. Single vs double quotes is a deeper shell topic, but remember this basic difference for lab: double quotes expand variables, single quotes do not.
- **Common mistake:** Using `echo` with unquoted variables containing spaces, and getting split output. Fix: quote variables in scripts, like `echo "$HOME"`, until quoting rules are clear from a shell guide.

---

## 9. Editing files in terminal — `nano` (beginner-friendly)

You will need to edit files: notes, scripts, settings in lab (with backup first for settings).

### 9.1 Why `nano` first?

- `nano` is a simple terminal text editor, with on-screen help at the bottom. It is easier for beginners than `vim`, which has modes and a steeper learning curve.
- Learn `nano` first, learn `vim` later if you want, after fundamentals are solid. No shame in using `nano` — professionals use the editor that lets them work accurately, not the hardest editor.

### 9.2 Basic `nano` workflow (lab, test file only)

```bash
nano ~/lab-test/notes.txt
```

Inside `nano`:
- Type normally to write text.
- Arrow keys to move.
- `Ctrl+O` = Write Out (save). It will ask filename — press Enter to confirm same filename.
- `Ctrl+X` = Exit. If you changed file and did not save, `nano` will ask "Save modified buffer?" — press `Y` for yes, `N` for no, `Ctrl+C` to cancel exit and go back to editing.
- `Ctrl+K` = Cut a line. `Ctrl+U` = Uncut (paste) a line. Useful, not required on day one.
- Bottom of screen shows these shortcuts — read them, do not memorise all at once.

**Lab task:**
1. `nano ~/lab-test/notes.txt`
2. Type: "This is my first nano note. I made this file myself in lab."
3. Save with `Ctrl+O`, Enter.
4. Exit with `Ctrl+X`.
5. Back in terminal, run `cat ~/lab-test/notes.txt` to verify your text saved correctly.

**Common mistakes:**
- Forgetting to save (`Ctrl+O`) before exit (`Ctrl+X`), then answering `N` to save prompt, and losing work. Fix: save first, then exit, then verify with `cat`.
- Editing a system settings file in `nano` without backup first. Fix: `cp file file-backup` before editing any settings file in lab, and change only one line at a time, with a note of what you changed.

---

## 10. Algorithm — How to learn any new command (use for every command above)

```
ALGORITHM: LEARN_A_COMMAND

INPUT: A command name, for example "grep"
OUTPUT: Notes + lab proof + ability to use it tomorrow

1. PURPOSE
   - Run: command --help, and read first 10 lines.
   - Write in notes: "This command does ______. I use it when ______."
   - If you cannot write purpose in one sentence, read --help again, slower.

2. EXAMPLE IN LAB
   - Create a test file/folder in ~/lab-test, with test data you made.
   - Run the command on that test data only, with one simple option first.
     Example: ls -l, grep "word" file, head -20 file.
   - Save command + output in notes, exactly as run.

3. OUTPUT MEANING
   - For each output line/column, write what it means.
   - Ask: "What changed if I change one option?" Test one option change at a time.
     Example: ls vs ls -l vs ls -a — what changed in output, and why?

4. COMBINE IT
   - Combine with a pipe: command | grep, command | head, command | wc -l.
   - Note: what did the pipe add? Filter? Count? Shorten?

5. MISTAKES
   - Try one common mistake safely in lab, note the error, and note the fix.
     Example for rm: list with ls *.txt first, before rm *.txt, in test folder only.
   - Write mistake + fix in notes. Mistakes noted are mistakes not repeated.

6. EXPLAIN IT
   - Explain the command in 1 minute, in simple English, without notes:
     "This command is ______. I use it when ______. Example: ______. Output means ______."
   - If you cannot explain it, repeat Steps 1-3 today, not tomorrow.

7. REVIEW TOMORROW
   - Next day, try the command again without notes.
   - If you can use it correctly, mark it DONE. If not, repeat Steps 1-3.

END ALGORITHM
```

**Practical design note:**
This algorithm works for commands, for tools, and later for red-team techniques. The pattern is always: purpose, example, output meaning, combine, mistakes, explain, review. Learn the pattern once, reuse it for every new tool for the rest of your career.

---

## 11. Lab tasks — full practice set, own machine only

Do these in order, in `~/lab-test`, with test files you make. Save outputs in notes, and explain each output. Do not skip explanation.

**Task Set A — Navigation:**
1. `pwd`, `ls`, `ls -l`, `ls -a` in home, in lab-test, in a subfolder you create.
2. `cd` between home, lab-test, subfolder, back with `cd ..`, back with `cd -`, home with `cd ~`.
3. Write in notes: each command, what it did, and what folder you ended in after each step.

**Task Set B — Reading:**
1. Create a test file with 5 lines, using `echo` and `>>` (append, not replace).
2. Read it with `cat`, `head -3`, `tail -3`, `less` (quit with `q`).
3. Search it with `grep` for one word, case-sensitive, then case-insensitive with `-i`.
4. Count matching lines with `grep "word" file | wc -l`.

**Task Set C — Create, copy, move, delete (test only):**
1. `mkdir -p ~/lab-test/practice/a/b`
2. `touch ~/lab-test/practice/a/b/file.txt`, write a line into it with `echo`.
3. `cp` file to a backup name, verify both exist with `ls -l`.
4. `mv` backup file into a different folder, verify move with `ls -l` in both folders.
5. Delete test files you no longer need, using safe delete algorithm: `pwd`, `ls -l`, `rm` exact name, `ls -l` again to verify.
6. Write in notes: what `cp` vs `mv` vs `rm` did differently, in your own words.

**Task Set D — Permissions practice:**
1. Create a test file, check `ls -l`, note original permissions.
2. `chmod 600`, check, explain. `chmod 644`, check, explain. `chmod 755` on a test folder, check with `ls -ld`, explain.
3. Explain why you would NOT use `chmod 777` on a real file, in notes, with the minimum-permission fix for "permission denied".

**Task Set E — System check (own machine only):**
1. `whoami`, `id`, `uname -a`, `hostname`.
2. `ps aux | head -20`, note 3 processes you recognise.
3. `ss -tln`, note localhost-only vs exposed ports, in lab terms.
4. Save a short system note: OS, user, processes noted, ports noted, with meanings.

**Task Set F — Combine commands:**
1. `ls -l ~/lab-test | grep ".txt"`
2. `ps aux | grep python3 | head -5`
3. `cat ~/lab-test/search.txt | sort | uniq | wc -l` (create search.txt with duplicate lines first, test data only).
4. Write in notes: for each combined command, what did the pipe add, and what was final output meaning?

**Task Set G — Edit with nano:**
1. Open `nano ~/lab-test/nano-note.txt`, write 3 lines in your own words about what you learned today.
2. Save with `Ctrl+O`, Enter, exit with `Ctrl+X`.
3. Verify with `cat ~/lab-test/nano-note.txt`.
4. If save failed, note what happened, fix it, and try again — that debug note is also learning.

---

## 12. Common mistakes — master list for this file

- **Skipping `pwd` and `ls -l` before delete/move/permission change.** Fix: make it a habit, like checking mirrors before driving. `pwd` + `ls -l` first, always.
- **Using `rm -rf` with wildcards or variables without listing first.** Fix: `ls` the exact pattern first, then `rm` only if list matches intent exactly, in test folder only.
- **Using `>` when you meant `>>`, and losing old content.** Fix: back up important files before redirecting, and remember: `>` = replace, `>>` = append.
- **Using `cat` for large files, flooding terminal.** Fix: `head`, `tail`, `less` for large files.
- **Forgetting quotes for `grep "hello world"` and `find -name "*.txt"`.** Fix: quote patterns with spaces or wildcards, always, until quoting rules are automatic.
- **Running `find /` and waiting forever.** Fix: start `find` in a specific folder you own, like `~/lab-test`.
- **Killing processes without learning what they are.** Fix: note PID and COMMAND, learn from docs, then decide.
- **Using `chmod 777` as a quick fix.** Fix: minimum-permission design, as in filesystem file.
- **Forgetting `q` to quit `less`, `man`, and `top`.** Fix: `q` = quit, for all three. Remember it on day one.
- **Copying a history command without checking folder and arguments.** Fix: read history line fully, check `pwd`, check arguments, then run again if still correct.
- **Memorising command names without purpose, example, and output meaning.** Fix: use Learn-A-Command Algorithm above for every new command, one command at a time, deeply.

---

## 13. Checklist — can you move to Networks?

Do not move to `03-networks/` until you can check these boxes honestly:

- [ ] I can use `pwd`, `ls`, `ls -l`, `ls -a`, `ls -ld`, `cd`, `cd ..`, `cd -`, `cd ~` confidently, and I can explain the difference between absolute and relative paths.
- [ ] I can read files with `cat`, `less` (quit with `q`), `head`, `tail`, and I know when to use each.
- [ ] I can create, copy, move, and safely delete test files/folders, with `pwd` + `ls -l` before and after, and I can explain `cp` vs `mv` vs `rm`.
- [ ] I can search with `grep` (including `-i`, `-r`) and find files with `find` (in my own lab folders, with quoted patterns).
- [ ] I can change permissions with `chmod` (numbers and `u+x` form), check with `ls -l`, and explain why 777 is almost never correct.
- [ ] I can check who I am (`whoami`, `id`), system info (`uname -a`, `hostname`), processes (`ps aux`), and localhost ports (`ss -tln`, own machine only), and explain each output.
- [ ] I can combine commands with pipes (`|`, `grep`, `head`, `wc -l`, `sort`, `uniq`) and explain what the pipe added.
- [ ] I can use redirection correctly: `>` = replace, `>>` = append, and I can explain the difference with a lab example where I did not lose data by mistake.
- [ ] I can edit a file in `nano`, save with `Ctrl+O`, exit with `Ctrl+X`, and verify with `cat`.
- [ ] I recorded a 3-minute explanation of "My 10 most useful Linux commands, with purpose and example for each", in simple English.

If a box is unchecked, that command goes back through the Learn-A-Command Algorithm, with lab tasks from Section 11. That is structured, not slow.

**Next file:** `03-networks/network-basics.md` and `network-tools.md` — how computers talk, IP, DNS, ports, TCP/UDP, HTTP, and network tools with output meaning, all in the same big, structured style, lab only. No shortcuts.

---

*Educational use only. All practice must be on systems you own or have written permission to test. Never delete, move, or change permissions on files you do not own or do not understand, and never read private files — permissions check only is enough for an audit.*
