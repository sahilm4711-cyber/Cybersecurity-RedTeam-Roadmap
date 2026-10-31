# Linux Commands — The Daily Set, Used Properly

Lab only, own machine only, test files you made yourself in `~/lab-test`. Before any delete, move, or permission change, run `pwd` and `ls -l` and confirm you are in the folder you think you are in. Never read private files, shadow data, or private keys — permissions check only is enough for an audit.

You already know what a computer is and how the filesystem is laid out. This file is about moving in it: navigating, reading, creating, finding, fixing permissions, and combining commands into something useful. Learn purpose first, then one example, then what the output actually means. Names alone fade in a week; purpose plus a lab run sticks.

## How a command is built

Almost every command follows the same shape:

```
command [options] [arguments]
```

The command is what to do (`ls` lists, `cat` shows a file). Options, usually starting with `-`, say how to do it (`ls -l` lists in long form with permissions, `ls -a` includes hidden files). Arguments say what to do it to (`ls /home` lists that folder, `cat notes.txt` shows that file).

Professionals don't memorise every option. They remember the purpose and check the details when they need them:

```bash
ls --help
man ls
```

`--help` is the short version, usually enough to start. `man` is the full manual; press `q` to quit. That habit — purpose in memory, details in documentation — is worth more than memorising fifty options for `ls`.

## Where am I, and what is here? — pwd, ls, cd

These three are your navigation system, used every day in every lab, before anything else.

**`pwd` — print working directory.** It shows the full path of where you are standing. Run it before any create, move, delete, or permission change, every time, especially in a fresh terminal. Skipping it and assuming you are in the right folder is how the wrong file gets deleted.

```bash
pwd
```

Output is a path, like `/home/your-user`. That is the whole answer, and it is the answer you check before doing anything destructive.

**`ls` — list.** What exists here, before you open, move, or delete anything — and again after, to verify what changed.

```bash
ls
ls -l
ls -a
ls -l ~/lab-test
ls -ld ~/lab-test
```

Plain `ls` gives names only. `ls -l` is the audit form: permissions, owner, group, size, time, name, one per line — always use `-l` when checking permissions. `ls -a` includes hidden files starting with `.`, like `.bashrc`; hidden means settings file, not secret. `ls -l ~/lab-test` lists that folder even if you aren't in it. `ls -ld ~/lab-test` gives details of the folder itself, not its contents — the `d` means directory itself, and mixing these two up is a common source of confusion when checking a folder's own permissions.

Reading one `ls -l` line (full detail is in `linux-filesystem.md`):

```
-rw-r--r-- 1 your-user your-user 38 Nov  1 12:00 test.txt
```

`-` is a regular file, `rw-` is owner read and write, `r--` is group read only, `r--` is others read only, then owner, group, size in bytes, and name.

**`cd` — change directory.** Move to where the work is, so you can use short relative paths instead of full paths everywhere.

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

`cd ~/lab-test` goes to your lab folder (`~` is home). `cd ..` goes up one folder. `cd -` goes back to the previous folder — handy when jumping between two places. `cd ~` and bare `cd` both go home.

Two things bite beginners here. Linux is case-sensitive: `Lab-Test` and `lab-test` are different folders, so use `ls` to see the exact spelling before you `cd`. And if `cd` fails with "no such file or directory", you stayed where you were — run `pwd` to confirm you actually moved before running the next command in what you think is the new folder. A failed `cd` followed by commands run in the old folder is a classic silent error.

## Reading files — cat, less, head, tail

**`cat` — show a whole text file.** For small files only: notes, settings you understand, test files you made.

```bash
mkdir -p ~/lab-test
echo "Hello lab, this is a test line." > ~/lab-test/a.txt
cat ~/lab-test/a.txt
```

`echo "text" > file` writes text into a file, creating it if needed and replacing old content if it already existed. `>` replaces — that distinction matters, see the redirection section below. The `cat` output is just the file content, exactly as written.

Don't `cat` a large log and flood your screen, don't `cat` a binary file and guess at unreadable output, and don't `cat` private files you shouldn't be reading.

**`less` — read page by page.** For large files, like logs, where `cat` would flood the screen.

```bash
less ~/lab-test/a.txt
```

Inside `less`: Space or PageDown for the next page, `b` for the previous page, `/word` then Enter to search forward, and `q` to quit. Forgetting `q` and closing the terminal instead is a rite of passage — just remember `q` quits `less`, `man`, and `top` alike.

**`head` and `tail` — first lines, last lines.** Logs grow large, and usually you care about the start of a file or the most recent lines at the end.

```bash
head -20 ~/lab-test/a.txt
tail -20 ~/lab-test/a.txt
tail -f ~/lab-test/a.txt
```

`head -20` is the first twenty lines, `tail -20` the last twenty — change the number to whatever you want. `tail -f` follows live, showing new lines as the file grows; press `Ctrl+C` to stop. Reach for `head` or `tail` first on a big file, then `less` if you need to search inside it, instead of scrolling through ten thousand lines of `cat` output.

## Creating, copying, moving, deleting — mkdir, touch, cp, mv, rm

Powerful commands, so the `pwd` and `ls -l` check before them is a rule, not a suggestion — especially for `mv` and `rm`.

**`mkdir` — make a directory.**

```bash
mkdir ~/lab-test/myfolder
mkdir -p ~/lab-test/a/b/c
```

The first creates one folder if its parent exists. `-p` creates parents too, and doesn't error if the folder already exists — so `mkdir -p ~/lab-test/a/b/c` builds the whole chain in one command. For nested paths, `-p` is the safe default; without it you get "no such file or directory" because the parent doesn't exist yet.

**`touch` — create an empty file, or update a time.**

```bash
touch ~/lab-test/notes.txt
ls -l ~/lab-test/notes.txt
```

A new file shows size 0, which is normal, not broken. `touch` doesn't write content — it creates an empty placeholder or updates an existing file's time without changing content. To write content, use an editor like `nano` or `echo > file` for a simple line.

**`cp` — copy.** Original stays where it was.

```bash
cp ~/lab-test/a.txt ~/lab-test/a-backup.txt
cp -r ~/lab-test/myfolder ~/lab-test/myfolder-copy
```

Copy a file to a backup name before a risky change in a lab — change the copy, keep the original safe. For folders you need `-r`, recursive, to copy everything inside including subfolders; without it `cp` errors on a folder, which is a safety feature, not a bug. Check the destination with `ls -l` first, so you don't silently overwrite a file you meant to keep.

**`mv` — move, or rename.** Unlike `cp`, the original disappears from its old place.

```bash
mv ~/lab-test/a.txt ~/lab-test/b.txt
mv ~/lab-test/b.txt ~/lab-test/myfolder/
```

The first renames `a.txt` to `b.txt` in the same folder. The second moves `b.txt` into `myfolder`, keeping its name. After each, verify with `ls -l` in both the old and new locations — moving a file to the wrong folder because you didn't check `pwd` first, then not finding it later, wastes more time than the check ever will.

**`rm` — delete, permanently.** Terminal deletes don't go to a trash bin, and they usually can't be undone. Only ever in test folders you created yourself, for files you are sure you don't need or have backed up.

```bash
mkdir -p ~/lab-test/delete-me
touch ~/lab-test/delete-me/temp.txt
ls -l ~/lab-test/delete-me/
rm ~/lab-test/delete-me/temp.txt
ls -l ~/lab-test/delete-me/
rm -r ~/lab-test/delete-me
ls -l ~/lab-test/
```

`rm file` deletes one file. `rm -r folder` deletes a folder and everything inside it recursively — `-r` is required for folders, as with `cp -r`. The final `ls` confirms the folder is gone and nothing else went with it.

The forms to never type casually: `rm -rf /` would try to delete everything from root, `rm -rf ~` your home folder, `rm -rf *` everything in the current folder — which is exactly why `pwd` first matters — and `rm -rf $FOLDER` with a variable you haven't checked, which could be empty and point somewhere you never intended. Check a variable with `echo $FOLDER` before it goes near `rm`.

A safe delete routine, every time: `pwd` — where am I? `ls -l` — what is here, exactly? Is this a test file I made, with a backup if needed? If yes to all, `rm` that exact name, with the full path if possible, like `rm ~/lab-test/delete-me/temp.txt`. Then `ls -l` again to verify it is gone and nothing else is. Same discipline for wildcards: run `ls *.txt` first to see exactly what matches, and only then `rm` if that list is exactly what you intend to delete.

## Searching and finding — grep, find, which, history

**`grep` — search text inside files.** It prints only the lines that match, which is the whole point: filter to what matters instead of reading the whole file.

```bash
echo "hello lab" > ~/lab-test/search.txt
echo "second line with test word" >> ~/lab-test/search.txt
echo "third line" >> ~/lab-test/search.txt
grep "lab" ~/lab-test/search.txt
grep -i "LAB" ~/lab-test/search.txt
grep -r "hello" ~/lab-test/
```

The three `echo` commands build a three-line test file; `>>` appends without replacing, unlike `>`. `grep "lab"` is case-sensitive — `lab` matches, `LAB` doesn't. `grep -i` ignores case. `grep -r` searches recursively through a folder and its subfolders. The first grep above prints just `hello lab`, one line, not the whole file.

Quote patterns with spaces or wildcards, always: `grep "hello lab" file`, not `grep hello lab file`, which the shell reads as separate arguments. And `grep` is for text files — on a binary file it gives unreadable output, so use the right tool for the file type.

**`find` — find files by name, type, time, and more.**

```bash
find ~/lab-test -name "*.txt"
find ~/lab-test -type f
find ~/lab-test -type d
```

The first finds names ending in `.txt`, the second files only, the third folders only. Quote the pattern — `find -name "*.txt"` with quotes, so the shell doesn't expand `*` in the current folder before `find` sees it. And start `find` in a specific folder you own, like `~/lab-test`, not `/`: searching the whole system takes forever and buries you in output.

**`which` — where is this command?**

```bash
which python3
which ls
ls -l $(which python3)
```

The first two print paths like `/usr/bin/python3`. The third lists details of that file — permissions, owner. `which` answers where, not what; `--help` and `man` answer what and how. When a command behaves unexpectedly, checking which copy you are actually running is a sensible first step.

**`history` — what did I run before?**

```bash
history | tail -20
```

The last twenty commands from this terminal's history — useful for recalling a long command, repeating a successful one, or checking what you ran just before an error when writing up lab notes. Read a history line fully and check `pwd` and arguments before rerunning it; a command that was right yesterday in another folder can be wrong today.

## Permissions commands — chmod, chown, whoami

Full permission theory is in `linux-filesystem.md`. Here is the command practice, test files only.

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

The original is often 644. `chmod 600` makes it owner read/write only — private design. `chmod 644` puts it back to owner read/write, others read. `chmod u+x` is symbolic form: add execute for the owner, taking it to 744. For a text file that `x` is usually pointless — the example just shows symbolic form working. Always `ls -l` before and after `chmod`, so you see the change.

Don't use `chmod 777` as a fix for "permission denied". Check the owner with `ls -l`, check who you are, work out the one permission you actually need, and change only that, to the minimum. And don't practise permission changes on system files — test files in `~/lab-test` until the logic is automatic.

`chown` changes who owns a file, and normally requires root; doing it wrong can lock the original owner out. Know the difference: `chmod` changes permissions (mode), `chown` changes owner. Different jobs, different commands.

Before debugging any permission problem, check who you are:

```bash
whoami
id
groups
```

`whoami` prints your username. `id` prints user ID, group ID, and groups in numbers and names. `groups` prints group names only. Permissions depend on who you are and which groups you're in — if a file is owned by a group you're in, group permissions apply to you; if not, others permissions apply. Debugging permissions without checking `whoami` first is guessing.

## System info and processes — uname, hostname, ps, ss, top

```bash
uname -a
hostname
```

`uname -a` prints kernel name, version, machine type, and architecture like `x86_64` in one line — note it in an audit report rather than memorising version numbers. `hostname` prints the machine's name.

```bash
ps aux | head -20
ps aux | grep python3
```

The first lists all processes, first twenty lines for readability. The second filters to lines containing `python3` — and note that the `grep python3` process itself shows up in its own output, because grep is running while `ps` runs. Ignore that line. Columns to read: USER (owner), PID (process ID), %CPU, %MEM, COMMAND (what started it). Don't kill a process by PID before learning what it is from documentation.

```bash
ss -tln
```

Own machine only. `-t` TCP, `-l` listening, `-n` numbers not names. `127.0.0.1:5432` is localhost only, safer by design. `[::1]:5432` is the IPv6 localhost equivalent. `0.0.0.0:5432` is all IPv4 networks — check it carefully in a lab design. An open port alone doesn't mean vulnerable; it means a service is present. The next questions are what service, what version, whether it should be open, to whom, and whether it is patched.

`top` shows processes live, updating, sorted by CPU or memory use. `htop` is a friendlier version if installed in a lab. Press `q` to quit — don't press other keys randomly inside `top` until you've read `man top`, since some keys can kill processes, and you don't want to kill one by accident.

## Combining commands — pipes, counts, sorting, chaining

Single commands are tools. Combined commands are a workflow, and this is where Linux starts feeling powerful.

A pipe, `|`, sends one command's output into the next as input:

```bash
ls -l ~/lab-test | grep ".txt"
ps aux | grep python3
ps aux | head -20
cat ~/lab-test/search.txt | grep "lab"
```

List, then filter to `.txt` lines. List processes, then filter to Python. List processes, then show the first twenty lines. Show a file, then filter to lines with `lab`. The design pattern is Unix philosophy in miniature: small tools, each doing one job well, chained together, instead of one huge tool doing everything badly.

`wc` counts what comes through the pipe:

```bash
grep "lab" ~/lab-test/search.txt | wc -l
ls ~/lab-test | wc -l
```

The first counts matching lines (a number, like `1`); the second counts names listed in the folder. `sort` sorts lines alphabetically and `uniq` removes duplicate adjacent lines, so they usually go together — `sort file | uniq`, and `sort file | uniq | wc -l` counts unique lines. Handy for pulling unique values out of a lab log you own.

For running commands in sequence, `&&` and `;` differ in a way that matters:

```bash
mkdir -p ~/lab-test/chain && cd ~/lab-test/chain && pwd
cd ~/lab-test ; ls -l
```

`&&` runs the next command only if the previous one succeeded. `;` runs the next regardless. For dependent steps — create a folder, then go into it — `&&` is the safer design: if `mkdir` fails, `cd` never runs, and you don't end up working in the wrong folder. With `;`, a failed `cd` still runs the `ls` in the old folder, and the output can mislead you if you aren't checking. Use `&&` for dependent steps, `;` only when steps are truly independent, and verify with `pwd` after any `cd`.

## Redirection — >, >>, and echo

```bash
echo "first line" > ~/lab-test/out.txt
cat ~/lab-test/out.txt
echo "second line" > ~/lab-test/out.txt
cat ~/lab-test/out.txt
```

After the first `echo`, the file holds "first line". After the second `echo` with `>`, it holds only "second line" — the first line is gone, replaced. That is both the use and the danger of `>`: replace, not add. Using `>` when you meant to add, and losing old content, is a mistake you make once and then remember.

```bash
echo "first line" > ~/lab-test/out2.txt
echo "second line" >> ~/lab-test/out2.txt
echo "third line" >> ~/lab-test/out2.txt
cat ~/lab-test/out2.txt
```

With `>>`, append, all three lines survive in order. Append is how you build a report line by line in a script, or collect outputs from several commands into one lab report file. Back up anything important before redirecting into it.

`<` is input from a file — `grep "lab" < ~/lab-test/search.txt` feeds the file to grep instead of typing lines by hand. In practice most commands take a filename argument directly, so `<` is less common for beginners, but the direction is worth remembering: `>` points where output goes, `<` points where input comes from. Arrow direction is data flow direction.

`echo` itself just prints text, or writes it into a file with redirection:

```bash
echo "Hello lab"
echo $HOME
echo "Test line" > ~/lab-test/echo-test.txt
```

`echo $HOME` prints your home path because `$HOME` is a variable. Double quotes still expand variables; single quotes don't — `echo '$HOME'` prints the literal text `$HOME`. Quote variables in scripts, like `echo "$HOME"`, until quoting rules are automatic, especially when values might contain spaces.

## Editing in the terminal — nano

You will need to edit notes, scripts, and lab settings (backed up first, for settings). Start with `nano` — it shows its shortcuts on screen, and it is easier than `vim`, which has modes and a steeper learning curve. No shame in `nano`; professionals use the editor that lets them work accurately.

```bash
nano ~/lab-test/notes.txt
```

Type normally, move with arrow keys. `Ctrl+O` writes out (saves) — it asks for a filename, press Enter to confirm the same one. `Ctrl+X` exits; if you changed the file without saving, it asks "Save modified buffer?" — `Y` for yes, `N` for no, `Ctrl+C` to cancel and go back to editing. `Ctrl+K` cuts a line, `Ctrl+U` pastes it back.

Try it: open `nano ~/lab-test/notes.txt`, write a few lines about what you learned today, save with `Ctrl+O` and Enter, exit with `Ctrl+X`, and verify with `cat ~/lab-test/notes.txt`. If the save failed, note what happened and fix it — that debug note is learning too. The common loss here is forgetting to save before exit and answering `N` at the prompt. Save first, exit second, verify with `cat`.

## Learning any new command, the short version

The pattern that works for every command in this file, and later for every tool: read `--help` and write its purpose in one sentence. Run it once on test data in `~/lab-test` with one simple option, and save the command and its output in your notes. Read the output line by line and change one option at a time to see what changes. Combine it with a pipe — `| grep`, `| head`, `| wc -l` — and note what the pipe added. Try one common mistake safely in the lab, note the error and the fix. Then explain the command in a minute without notes: what it is, when you use it, an example, what the output means. If you can't explain it, repeat the lab run today, not tomorrow, and try it again the next day without notes to see if it stuck.

## A full practice pass

Do this in order, in `~/lab-test`, with test files you make, and explain each output as you go:

Navigation: `pwd`, `ls`, `ls -l`, `ls -a` in home, in lab-test, in a subfolder you create; `cd` between them, back with `cd ..`, back with `cd -`, home with `cd ~`. Note each command, what it did, and where you ended up.

Reading: build a five-line test file with `echo` and `>>`, read it with `cat`, `head -3`, `tail -3`, and `less` (quit with `q`), search it with `grep` case-sensitively and then with `-i`, and count matches with `grep "word" file | wc -l`.

Create, copy, move, delete: `mkdir -p ~/lab-test/practice/a/b`, `touch` a file inside it and write a line into it, `cp` it to a backup name and verify both exist, `mv` the backup into another folder and verify in both places, then delete test files you no longer need with the safe routine — `pwd`, `ls -l`, `rm` the exact name, `ls -l` again. Write down what `cp` versus `mv` versus `rm` did differently, in your own words.

Permissions: create a test file, note its original permissions, `chmod 600` and explain, `chmod 644` and explain, `chmod 755` on a test folder and check with `ls -ld`, and explain why you wouldn't use `chmod 777` on a real file, with the minimum-permission fix for "permission denied".

System check, own machine only: `whoami`, `id`, `uname -a`, `hostname`, `ps aux | head -20` with three processes you recognise, and `ss -tln` with localhost-only versus exposed ports noted in lab terms.

Combining: `ls -l ~/lab-test | grep ".txt"`, `ps aux | grep python3 | head -5`, and `cat ~/lab-test/search.txt | sort | uniq | wc -l` on a file with duplicate lines. For each, note what the pipe added and what the final output meant.

Editing: write three lines in `nano`, save with `Ctrl+O`, exit with `Ctrl+X`, verify with `cat`.

When you can use `pwd`, `ls` in all its useful forms, `cd` in all its forms, and explain absolute versus relative paths; read with `cat`, `less`, `head`, and `tail` and know when each fits; create, copy, move, and safely delete test files and explain the differences; search with `grep` including `-i` and `-r` and find files with `find` in your own lab folders with quoted patterns; change permissions with `chmod` and explain why 777 is almost never right; check who you are, system info, processes, and localhost ports and explain each output; combine commands with pipes and explain what each pipe added; use `>` versus `>>` correctly without losing data; and edit in `nano`, save, exit, and verify — you are ready for `03-networks/`.

---

*Lab only: systems you own or have written permission to test, test data only. Never delete, move, or change permissions on files you don't own or don't understand.*
