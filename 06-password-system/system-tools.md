# 06 — Password and System: System Tools, Fully Explained

## What this file is

This is Phase 5, part 2. You already built a Local System Audit in your first projects: it checked your own machine's OS, user, folder permissions, and localhost listening ports, and saved a report. Now you learn system tools in general — what they check, why, and how to use them safely, on your own machine only.

If you can run a system check on your own machine, read its output line by line, explain what it proves and what it does not prove, and write a safe summary with placeholders only, you understand system tools. If you only run a tool and copy its full output, you do not.

No shortcuts here. Each tool class gets: simple explanation, what, why, when, when NOT to use it, lab example on your own machine only, output meaning, and common mistakes.

**Safety rule for this whole file:**
System tools here are for your own machine only.
- Do not run a system audit, scanner, or checker against another person's machine, a school machine, a company machine, or a remote host, without written permission.
- Do not read private file contents, private keys, or password files, even on your own machine, for a learning check. Check permissions, names, and numbers only — never contents of private files.
- Never put a real username, hostname, real IP, real file path with personal data, or real system details in a note, screenshot, or public file. Use generic placeholders: `your-user`, `<your-lab-ip>`, `<lab-folder>`, `<test-file>`.
- A system report saved on your own machine is a private document. Do not publish it to a public repo without first removing all personal details and getting explicit approval for that exact file — as you learned with the audit report decision in your first projects.

---

## 1. What is a system audit, in simple words?

**Simple explanation:**
A system audit is a careful check of your own machine, at one point in time, answering: What is this machine? Who can use it? What is running? What is listening? What files and folders have which permissions? What is out of date, or left open by mistake?

**Practical design note:**
An audit is not a hack, and it is not a fix. It is a report. A good audit does three things, in order:
1. Collects facts, read-only, without changing the system.
2. Compares those facts to a simple, safe baseline you wrote down: What should be listening? What permissions should a lab folder have?
3. Writes a short summary: what matches the baseline, what does not, and what is the next action, in your own lab.

**Algorithm: AUDIT_MY_OWN_MACHINE**

```
Step 1: Question: What am I checking today, on my own machine only?
Step 2: Baseline: Write down what should be true, before you run anything. Example: "Only localhost ports should be listening, lab folders should be 755 or stricter."
Step 3: Collect, read-only: OS, user, folder permissions, listening ports, running services, in lab terms.
Step 4: Compare each fact to the baseline. Mark: matches, does not match, or unknown.
Step 5: For each "does not match", ask: Do I need this service or permission in lab? If not, what is the safe lab action? (Stop a lab service, tighten a lab folder permission, in your own lab only.)
Step 6: Write a summary with placeholders only, no real personal details.
Step 7: Clean up: stop lab services you do not need, do not publish the full report publicly.
```

**Common mistake:**
Running an audit tool, getting a long report, and thinking the report itself fixed something. A report fixes nothing until you act on one finding, in your own lab, and re-check it.

---

## 2. Operating system and user checks — what machine is this, who am I?

**What:**
These checks answer: What OS is this? What version? Who is the current user? What groups is that user in? They are the first lines of any audit, because every other fact depends on them.

**Why:**
You cannot understand a permission, a port, or a service without knowing whose machine it is, and what OS it runs. A permission that is safe on one OS may be different on another, and a service that is normal on one machine may be unexpected on another.

**When:**
At the start of any audit, on your own machine only, before you check ports, files, or services.

**When NOT to use it:**
Do not use these checks to gather information about another person's machine, and do not share the full output publicly, with real hostnames or usernames.

**Lab example — own machine only:**

```bash
uname -a
whoami
id
hostname
```

**Output meaning, in general terms, not with real values:**
- `uname -a` = OS name, kernel version, and machine type, in general terms. In a public note, write "Linux, lab machine" instead of the full kernel string with a real hostname.
- `whoami` = current username, on your own machine. In a public note, write `your-user`, not the real username.
- `id` = user ID, group ID, and groups for that user. Groups matter for permissions: if a file is owned by a group you are in, group permissions apply to you; otherwise, others permissions apply.
- `hostname` = machine name, on your own machine. In a public note, write `<lab-host>` or omit it, never the real hostname.

**Common mistakes:**
- Sharing a full `uname -a` + `hostname` + `whoami` output publicly. That is a fingerprint of your machine. Summarise with placeholders.
- Thinking a hostname is a secret to hide from yourself. It is not secret from you, on your own machine — it is only something you should not publish publicly.

**Practice task:**
Run these four commands on your own machine only. Write a one-line summary, with placeholders only: OS type in general terms, current user as `your-user`, and whether you are in any unexpected groups, in your own lab terms.

---

## 3. Folder and file permission checks — who can read, write, and run what?

**What:**
These checks answer, for a lab folder or file you own: What are its permission numbers and letters? Who owns it? Who can read it, write to it, and run it?

**Why:**
Permissions are the first defence for files. A lab folder that everyone can write to is a door anyone can use. A private file that everyone can read is a leak, even if no one has read it yet.

**When:**
When you create a lab project, after you download or create a lab file, or when an audit shows a permission that does not match your baseline.

**When NOT to use it:**
Do not change permissions on files you do not own, do not read private file contents to "check" them, and do not use permission checks to peek into other users' folders without permission.

**Lab example — own lab folder only:**

```bash
ls -ld <lab-folder>
ls -l <lab-folder>
stat <lab-folder>/<test-file>
```

Replace `<lab-folder>` with a lab folder you created yourself, and `<test-file>` with a test file you created yourself, on your own machine only.

**Output meaning, in general terms:**
- `ls -ld` = permissions, owner, group, and name for a folder itself, not its contents.
- `ls -l` = permissions, owner, group, size, and name for each file in a folder.
- Permission letters: `r` = read (4), `w` = write (2), `x` = execute / enter a folder (1). Owner, group, others, in that order.
- Permission numbers, simply: `755` = owner rwx, group r-x, others r-x. `644` = owner rw-, group r--, others r--. `600` = owner rw-, group ---, others ---, for a private file. `775` = owner rwx, group rwx, others r-x, common for a shared lab folder, but not for a private file.
- `stat` = more detail: size, permissions in numbers and letters, owner, group, and times, for one file, in lab terms.

**Algorithm: CHECK_LAB_FOLDER_PERMISSIONS**

```
Step 1: Baseline: What should this lab folder's permissions be? Write it down first.
Step 2: Run ls -ld on that lab folder only, on your own machine.
Step 3: Compare: Do the numbers match the baseline?
Step 4: If they are too open (for example, a private lab file is readable by others), tighten them in your own lab only, with chmod, to the baseline.
Step 5: Re-run ls -ld. Confirm the new permissions match the baseline.
Step 6: Write a summary with placeholders only: folder as <lab-folder>, permissions as numbers, verdict as matches / fixed.
```

**Common mistakes:**
- Using `777` (everyone can read, write, and run) to "fix" a permission problem in lab. That is not a fix, it is removing all protection. Use the smallest permission that still lets your lab work.
- Changing permissions recursively (`chmod -R`) on a large folder without understanding what it contains. In lab, change one folder or file at a time, and check after each change.
- Reading a private key file or a password file to "check" it. Check its permissions only, never its contents.

**Practice task:**
Create a lab folder and a test file in it, on your own machine only. Check its permissions with `ls -ld` and `ls -l`, write down what they are, then set the test file to `600` in lab, re-check, and write down what changed, in your own words, with placeholders only.

---

## 4. Listening ports and running services — what is waiting, and what is running?

**What:**
These checks answer, on your own machine only: What services are listening for connections? What processes are running? Which of those should be running in lab, and which are unexpected?

**Why:**
A listening port is a door. A running process is a worker. An audit that does not check doors and workers is incomplete. A lab service left listening on all interfaces, or a lab process left running after lab ends, is a door or worker you forgot about.

**When:**
During any audit, after you install a lab service, and before you finish a lab session, to clean up.

**When NOT to use it:**
Do not check ports or processes on another machine, do not kill processes you do not own, and do not stop services on a system you do not own.

**Lab example — own machine only:**

```bash
ss -tln
ps aux | head
```

- `ss -tln` = TCP listening ports, numbers only, on your own machine. You met this in network tools: `127.0.0.1` = localhost only, safer; `0.0.0.0` = exposed to the network, check it.
- `ps aux | head` = a first look at running processes, in general terms. In a public note, do not copy a full process list with real usernames and paths. Summarise: how many lab processes are running, and are any unexpected, in your own lab terms?

**Output meaning, lab only:**
- A port listening on localhost only, for a lab service you started yourself, is normal in lab.
- A port listening on all interfaces, for a service you did not intend to expose, is a finding to fix in your own lab: stop the service, or bind it to localhost only, in lab.
- A process you do not recognise, on your own machine, is a question, not a verdict. Check what it is, from your lab setup and documentation, before you act. Do not kill a system process you do not understand.

**Common mistakes:**
- Killing a process because its name looks unfamiliar, on your own machine, without checking what it is first. Some system processes have unfamiliar names and are normal.
- Thinking a listening port means a system is hacked. It means a service is waiting. Whether that is a problem depends on the service, the address it listens on, and whether you need it in lab.
- Sharing a full `ss` or `ps` output publicly, with real IPs, hostnames, and paths. Summarise with placeholders.

**Practice task:**
Run `ss -tln` on your own machine only. Count localhost-only ports and exposed ports, separately. For each exposed port, write one sentence: what lab service is it, and do you need it in lab? Then stop any lab service you do not need, and re-run `ss -tln` to confirm it is gone.

---

## 5. Updates and outdated software — is my lab machine up to date?

**What:**
Update checks answer: Is my own machine's software up to date? Are there security updates I have not installed in lab? Are there old, known-broken components still in use in a lab project?

**Why:**
Outdated software is a common, boring, and real risk (see OWASP A06). A system that is not updated will, over time, run software with known flaws, even if it was safe when it was first installed.

**When:**
Regularly, on your own machine only, in lab terms, and before you start a new lab project, so you do not build on an old base.

**When NOT to use it:**
Do not update a system you do not own, do not update everything at once without a test in lab, and do not ignore an update because "it works now". In lab, update one component at a time, test that the lab still works, and note what you changed.

**Lab concept — own machine only, in general terms:**
- Check, with your own system's update tool, in lab terms, whether updates are available for your own machine. Do not copy the full update list with real package names and versions into a public note, unless those packages are public and generic — summarise: how many updates, and are any security updates, in general terms?
- For a lab project, keep a simple list: component name, version, and where you got it. Check that list from time to time, and update in lab first, one at a time.
- After an update in lab, re-run a small audit: `ss -tln` and a lab folder permission check, to confirm the lab still works as expected.

**Common mistakes:**
- Updating everything at once, in a hurry, with no test, and then not knowing which update broke the lab.
- Never updating at all, because updating feels risky. Not updating is also risky, over time, because known flaws accumulate.
- Sharing a full system update log publicly, with real hostnames and paths. Summarise with placeholders.

---

## 6. System audit reports — how to write a safe summary

**What:**
A system audit report is a short, written summary of an audit, for yourself, or for a lab project, with findings and next actions. It is not a copy of a tool output.

**How to write a safe summary, lab only:**
1. Title and date, in general terms: "Own-machine audit, lab, <date>".
2. Scope, in one sentence: "My own machine only, lab folders and localhost ports only."
3. Baseline, in one sentence: "Only localhost ports should be listening, lab folders should be 755 or stricter, private test files should be 600."
4. Findings, one line each, with placeholders only: "Port <lab-port>, localhost only, matches baseline." / "Port <lab-port>, exposed, lab service X, action: stop service in lab, re-checked, now gone."
5. Next actions, one line each, with no personal details.
6. What was NOT checked, in one line: "Private file contents, other machines, and remote hosts were not checked."

**What a safe report does NOT contain:**
- Real username, real hostname, real IP, real file paths with personal data, real process lists, or real system details that fingerprint your machine.
- Full tool output, copied without summarising.
- A recommendation to change a system you do not own.

**Practical design note, from your first projects:**
You learned this lesson with your own audit report: a report that is useful on your own machine can be harmful if published publicly with personal details in it. The rule you set for yourself — confirm the exact folder first, approve the exact package, and do not publish personal details — applies to every system report you will ever write. Keep that rule.

**Common mistakes:**
- Publishing a full audit report to a public repo, because it "looks professional". A professional report is summarised, with placeholders, and with personal details removed.
- Writing a report with findings, but no baseline and no next actions. A finding without a baseline is just a fact. A finding with a baseline and an action is useful.
- Forgetting to re-check after a fix. A fix is not done until you re-run the check and confirm the finding is gone, in your own lab.

**Practice task:**
Write a safe audit summary for your own machine, in your private notes only, using the format above, with placeholders only, for: OS in general terms, one lab folder permission, and two listening ports (one localhost only, one exposed, if you have one in lab, or "none exposed" if you do not). Do not publish it.

---

## 7. Common mistakes across system tools

- Auditing another machine, or a remote host, without written permission, because a tool allows it. Allowed by the tool is not permission.
- Reading private file contents, private keys, or password files, to "check" them. Check permissions and names only, never contents.
- Copying a full system output, with real details, into a public note, screenshot, or repo. Summarise with placeholders.
- Changing system settings, permissions, or services on a machine you do not own, or changing them on your own machine without understanding what they do, and without a baseline to re-check against.
- Thinking an audit report is a fix. A report is a list of findings. A fix is an action, in your own lab, followed by a re-check.
- Leaving lab services running, lab folders too open, or lab reports with personal details saved in a shared folder, after lab ends. Clean up at the end of every lab session.

---

## 8. Practice tasks — own machine only

1. Run the AUDIT_MY_OWN_MACHINE algorithm once, slowly, on your own machine only, with a baseline you wrote down first.
2. Check one lab folder's permissions with `ls -ld` and `ls -l`, set a test file to `600` in lab, re-check, and write what changed, with placeholders only.
3. Run `ss -tln` on your own machine only, count localhost-only vs exposed ports, and clean up any lab service you do not need.
4. Write a safe audit summary, in private notes only, using the format in Section 6, with placeholders only.
5. Read `safe-labs.md` next. For each safe-lab rule there, write one sentence about how it applies to your own system audits.

---

## 9. Checklist — you are ready to move on when you can do all of these

- [ ] I can explain, in simple words, what a system audit is, and what its three parts are.
- [ ] I can run the AUDIT_MY_OWN_MACHINE algorithm on my own machine only, with a baseline written down first.
- [ ] I can check OS, user, and groups on my own machine, and summarise them with placeholders only.
- [ ] I can check lab folder and file permissions, explain 755, 644, 600, and 775, and fix a lab permission in lab, with a re-check.
- [ ] I can check listening ports and running services on my own machine, tell localhost-only from exposed, and clean up a lab service I do not need.
- [ ] I can check, in general terms, whether my own lab machine and lab projects are up to date, and update one component at a time in lab, with a test.
- [ ] I can write a safe audit summary, with scope, baseline, findings, next actions, and what was NOT checked, using placeholders only.
- [ ] I have not audited, scanned, or changed another person's machine, and I have not read private file contents for a learning check.
- [ ] My notes contain no real username, hostname, real IP, real personal file path, or real system fingerprint — only generic placeholders.

**Next file:**
After this, read `safe-labs.md` in the same folder. It takes everything in this commit — passwords, system checks, and tools — and puts it into one set of lab rules you can use for every future project, safely.

---
*All examples in this file are for your own machine only. Never publish a real username, hostname, real IP, real personal file path, or real system fingerprint in a note or public file.*
