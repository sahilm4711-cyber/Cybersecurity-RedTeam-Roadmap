# System Tools

You already built a Local System Audit in your first projects: it checked your own machine's OS, user, folder permissions, and localhost listening ports, and saved a report. Now you learn system tools in general — what they check, why, and how to use them safely, on your own machine only.

If you can run a system check on your own machine, read its output line by line, explain what it proves and what it does not prove, and write a safe summary with placeholders only, you understand system tools. If you only run a tool and copy its full output, you don't.

Safety for this whole file: system tools here are for your own machine only. Don't run an audit, scanner, or checker against another person's machine, a school machine, a company machine, or a remote host, without written permission. Don't read private file contents, private keys, or password files, even on your own machine, for a learning check — check permissions, names, and numbers only, never contents of private files. Never put a real username, hostname, real IP, real file path with personal data, or real system details in a note, screenshot, or public file. Use placeholders: `your-user`, `<your-lab-ip>`, `<lab-folder>`, `<test-file>`.

A system report saved on your own machine is a private document. Don't publish it to a public repo without first removing all personal details and getting explicit approval for that exact file — as you learned with the audit report decision in your first projects.

## What a system audit actually is

A system audit is a careful check of your own machine, at one point in time, answering: what is this machine? Who can use it? What is running? What is listening? What files and folders have which permissions? What is out of date, or left open by mistake?

An audit is not a hack, and it is not a fix. It is a report. A good audit does three things, in order: it collects facts, read-only, without changing the system; it compares those facts to a simple, safe baseline you wrote down first — what should be listening, what permissions should a lab folder have; and it writes a short summary — what matches the baseline, what does not, and what the next action is, in your own lab.

So the working order is: decide what you are checking today, on your own machine only. Write the baseline down before you run anything — for example, "only localhost ports should be listening, lab folders should be 755 or stricter." Collect, read-only: OS, user, folder permissions, listening ports, running services. Compare each fact to the baseline and mark it matches, does not match, or unknown. For each mismatch, ask whether you need that service or permission in lab, and if not, what the safe lab action is — stop a lab service, tighten a lab folder permission, in your own lab only. Then write a summary with placeholders only, and clean up: stop lab services you don't need, and don't publish the full report publicly.

A report fixes nothing until you act on one finding, in your own lab, and re-check it. Running an audit tool and admiring a long report is not the work.

## OS and user checks: what machine is this, who am I?

These checks answer what OS this is, what version, who the current user is, and what groups that user is in. They are the first lines of any audit, because every other fact depends on them. You can't understand a permission, a port, or a service without knowing whose machine it is and what OS it runs.

On your own machine only:

```bash
uname -a
whoami
id
hostname
```

In general terms: `uname -a` gives OS name, kernel version, and machine type. In a public note, write "Linux, lab machine" instead of the full kernel string with a real hostname. `whoami` is the current username — in a public note, write `your-user`, not the real username. `id` gives user ID, group ID, and groups, and groups matter for permissions: if a file is owned by a group you are in, group permissions apply to you; otherwise, others permissions apply. `hostname` is the machine name — in a public note, write `<lab-host>` or omit it, never the real hostname.

Sharing a full `uname -a` plus `hostname` plus `whoami` output publicly is sharing a fingerprint of your machine. Summarise with placeholders. A hostname is not a secret from you, on your own machine — it is only something you should not publish publicly.

## Folder and file permissions: who can read, write, and run what?

These checks answer, for a lab folder or file you own: what are its permission numbers and letters, who owns it, and who can read it, write to it, and run it?

Permissions are the first defence for files. A lab folder that everyone can write to is a door anyone can use. A private file that everyone can read is a leak, even if no one has read it yet. Check permissions when you create a lab project, after you download or create a lab file, or when an audit shows a permission that doesn't match your baseline. Don't change permissions on files you don't own, don't read private file contents to "check" them, and don't use permission checks to peek into other users' folders without permission.

On your own lab folder only:

```bash
ls -ld <lab-folder>
ls -l <lab-folder>
stat <lab-folder>/<test-file>
```

`ls -ld` shows permissions, owner, group, and name for a folder itself, not its contents. `ls -l` shows the same for each file in a folder. The letters are `r` for read (4), `w` for write (2), `x` for execute or, for a folder, enter (1), in owner, group, others order. The numbers you'll see most: `755` is owner rwx, group r-x, others r-x. `644` is owner rw-, group r--, others r--. `600` is owner rw-, group none, others none — for a private file. `775` is owner rwx, group rwx, others r-x — common for a shared lab folder, but not for a private file. `stat` gives more detail: size, permissions in numbers and letters, owner, group, and times, for one file.

The method is the same as for an audit: write the baseline first, run `ls -ld` on that lab folder only, compare, and if the permissions are too open — a private lab file readable by others, for example — tighten them in your own lab only with `chmod`, to the baseline. Re-run `ls -ld` and confirm the new permissions match. Write a summary with placeholders only: folder as `<lab-folder>`, permissions as numbers, verdict as matches or fixed.

Two habits to avoid. Don't use `777` — everyone can read, write, and run — to "fix" a permission problem in lab. That is not a fix, it is removing all protection. Use the smallest permission that still lets your lab work. And don't change permissions recursively with `chmod -R` on a large folder without understanding what it contains. In lab, change one folder or file at a time, and check after each change. And never read a private key file or a password file to "check" it — check its permissions only, never its contents.

## Listening ports and running services: what is waiting, what is running?

These checks answer, on your own machine only: what services are listening for connections, what processes are running, which of those should be running in lab, and which are unexpected? A listening port is a door. A running process is a worker. An audit that doesn't check doors and workers is incomplete. A lab service left listening on all interfaces, or a lab process left running after lab ends, is a door or worker you forgot about.

On your own machine only:

```bash
ss -tln
ps aux | head
```

`ss -tln` lists TCP listening ports, numbers only, on your own machine. `127.0.0.1` means localhost only, safer; `0.0.0.0` means exposed to the network — check it. `ps aux | head` gives a first look at running processes. In a public note, don't copy a full process list with real usernames and paths. Summarise: how many lab processes are running, and are any unexpected, in your own lab terms?

Read it with judgement. A port listening on localhost only, for a lab service you started yourself, is normal in lab. A port listening on all interfaces, for a service you didn't intend to expose, is a finding to fix in your own lab: stop the service, or bind it to localhost only. A process you don't recognise, on your own machine, is a question, not a verdict — check what it is, from your lab setup and documentation, before you act. Don't kill a system process you don't understand; some system processes have unfamiliar names and are normal.

Likewise, don't mistake a listening port for a compromise. It means a service is waiting. Whether that is a problem depends on the service, the address it listens on, and whether you need it in lab. And don't share a full `ss` or `ps` output publicly, with real IPs, hostnames, and paths. Summarise with placeholders.

## Updates: is my lab machine up to date?

Update checks answer whether your own machine's software is up to date, whether there are security updates you haven't installed in lab, and whether there are old, known-broken components still in use in a lab project.

Outdated software is a common, boring, and real risk. A system that is not updated will, over time, run software with known flaws, even if it was safe when it was first installed. So check regularly, on your own machine only, and before you start a new lab project, so you don't build on an old base.

In general terms, on your own machine: check with your own system's update tool whether updates are available. Don't copy the full update list with real package names and versions into a public note unless those packages are public and generic — summarise how many updates there are, and whether any are security updates. For a lab project, keep a simple list — component name, version, where you got it — check it from time to time, and update in lab first, one at a time. After an update in lab, re-run a small audit — `ss -tln` and a lab folder permission check — to confirm the lab still works as expected.

Don't update everything at once, in a hurry, with no test, and then not know which update broke the lab. And don't never update at all because updating feels risky — not updating is also risky, over time, because known flaws accumulate.

## Writing a safe audit summary

A system audit report is a short, written summary for yourself or for a lab project, with findings and next actions. It is not a copy of a tool output.

A safe one, lab only, looks like this: title and date, in general terms — "Own-machine audit, lab, <date>". Scope, in one sentence — "My own machine only, lab folders and localhost ports only." Baseline, in one sentence — "Only localhost ports should be listening, lab folders should be 755 or stricter, private test files should be 600." Findings, one line each, with placeholders only — for example, "Port <lab-port>, localhost only, matches baseline," or "Port <lab-port>, exposed, lab service, action: stop service in lab, re-checked, now gone." Next actions, one line each, with no personal details. And what was not checked, in one line — "Private file contents, other machines, and remote hosts were not checked."

What it must not contain: a real username, real hostname, real IP, real file path with personal data, real process list, or real system details that fingerprint your machine; full tool output copied without summarising; or a recommendation to change a system you don't own.

You learned this lesson with your own audit report: a report that is useful on your own machine can be harmful if published publicly with personal details in it. The rule you set for yourself — confirm the exact folder first, approve the exact package, and don't publish personal details — applies to every system report you will ever write. Keep that rule. A finding without a baseline and without an action is just a fact. A finding with a baseline and an action is useful. And a fix is not done until you re-run the check and confirm the finding is gone, in your own lab.

Clean up at the end of every lab session, too: don't leave lab services running, lab folders too open, or lab reports with personal details saved in a shared folder.

Next: `safe-labs.md` — everything in this commit, passwords, system checks, and tools, put into one set of lab rules you can use for every future project.

---
*All examples here are for your own machine only. Never publish a real username, hostname, real IP, real personal file path, or real system fingerprint in a note or public file.*
