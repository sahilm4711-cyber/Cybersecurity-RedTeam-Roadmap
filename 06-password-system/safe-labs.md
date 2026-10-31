# 06 — Password and System: Safe Labs, Fully Explained

## What this file is

This is Phase 5, part 3, and it is the most important file in this commit — because it is the rulebook for everything after it. A safe lab is a place where you can learn, break things, and fix them, without harming anyone else, without leaking your own data, and without breaking the law or a school or company rule.

If you can build a safe lab, use it with a written plan, clean it up when you are done, and explain why each rule exists, in simple English, you are ready for later phases: tools, practice, and real projects, all lab-first.

No shortcuts here. Each rule gets: simple explanation, why it exists, how to do it in lab, and what happens if you skip it.

**Safety rule for this whole file — the one rule above all rules:**
Practise only on systems you own, or systems you have written permission to test, with test data only, in a lab you control.
- If you do not own it, and you do not have written permission, do not test it, do not scan it, do not probe it, and do not gather information about it for testing.
- "I only wanted to learn", "I did not change anything", "It was public", and "Everyone does it" are not permission. Written permission from the owner is permission.
- When in doubt, stop, and ask: Is this my own lab? If the answer is not a clear yes, do not start.

---

## 1. What is a safe lab, in simple words?

**Simple explanation:**
A safe lab is a controlled place, separate from your real life, where you can practise safely. It has three parts:
1. **Separate machines or folders:** A lab machine, a lab virtual machine (VM), or a lab folder, separate from your real documents, real accounts, and real personal files.
2. **Test data only:** Test usernames, test passwords as placeholders, sample files you created yourself, and sample websites you built yourself. No real passwords, no real personal data, no real customer data.
3. **Clear boundaries:** A written plan for what you will do in lab, what you will NOT do, and when you will stop and clean up.

**Everyday analogy:**
A safe lab is like a driving school car park, with cones, a teacher, and no real traffic. You can practise parking, turning, and stopping, make mistakes, and learn, without endangering anyone on a real road. Practising on a real road, without a licence and without permission, is not the same thing, even if you drive slowly.

**Practical design note:**
A lab that is not separate is not a lab. If your lab files sit in the same folder as your real documents, if your lab passwords are the same as your real passwords, or if your lab server is exposed to a real network, then a lab mistake can become a real leak. Separation is the first design rule, before any tool or technique.

---

## 2. The three kinds of safe lab, simply

### Kind 1: Your own machine, with a lab folder
What it is: A folder on your own machine, used only for lab, with only lab files in it. Example, in general terms: a folder named `lab-test` in your home folder, on your own machine only.
Good for: Python labs, password checkers with test data, system audits of your own machine, simple web servers on localhost only.
Limits: It shares the same machine as your real life, so you must be careful: lab files only in that folder, lab services only on localhost, and no real personal files in that folder.

### Kind 2: A virtual machine (VM), on your own computer
What it is: A whole computer, simulated inside your real computer, with its own OS, its own files, and its own network settings, separate from your real computer.
Good for: Testing a whole system, installing a deliberately vulnerable lab app, or practising system checks in a place you can reset, without touching your real machine.
Limits: A VM still uses your real computer's resources, and it still needs the same rules: test data only, lab network only, and snapshots or backups so you can reset it if a lab test breaks it.

### Kind 3: A cloud or rented lab, with your own account, for learning
What it is: A lab environment provided by a learning platform, for a course or a practice room, with its own machines, its own accounts, and its own rules, that you use only within that platform's scope.
Good for: Guided practice rooms, where the platform has already set up a safe target for you, with a clear scope and a clear end point.
Limits: The platform's scope is the permission. Do not go outside that scope, do not test the platform itself, and do not use a learning platform to test a real system outside it.

**Which kind should you start with?**
Start with Kind 1, your own machine with a lab folder, for Python, password, and system labs. Move to Kind 2, a VM, when you need a whole separate system, or a deliberately vulnerable lab app. Use Kind 3 only when a course or platform gives you a clear, written scope for a practice room, and stay inside that scope.

**Common mistake:**
Starting with the most complex lab, a VM with many machines and networks, before you can use a simple lab folder safely. A complex lab with no rules is less safe than a simple lab with clear rules. Start simple, with rules, and grow the lab only when you can explain why each new part is needed.

---

## 3. Building a safe lab, step by step

**Algorithm: BUILD_A_SAFE_LAB**

```
Step 1: Purpose: Write, in one sentence, what this lab is for. Example: "Learn Python password checking, with test data only."
Step 2: Separation: Choose a lab place, separate from real files. Example: a lab folder, or a lab VM, on your own computer only.
Step 3: Test data: Create test data only, in that lab place. Test usernames, test passwords as placeholders, sample files you made yourself.
Step 4: Network: Decide the network rule, before you start. For most labs in this roadmap: localhost only, or private lab network only, never a real, school, or company network for testing.
Step 5: Services: List the services you will run in lab, and only those. For each service, write: what it is, what port it uses in lab, and whether it listens on localhost only or is exposed, and why.
Step 6: Permissions: Set lab folder permissions to a safe baseline, in lab terms, before you start. Private lab files: 600. Shared lab folders: 755 or stricter, never 777 for a private file.
Step 7: Plan: Write a short plan: what you will do, what you will NOT do, and when you will stop. Example: "I will check my own lab folder permissions. I will NOT check another machine. I will stop after 30 minutes and clean up."
Step 8: Backup / reset: Note how you will reset the lab if a test breaks it. For a folder lab: delete lab files and recreate them. For a VM lab: use a snapshot, if your VM tool supports it, taken before the test.
Step 9: Start, with the plan in front of you. Do not start without a plan.
```

**Lab example — a simple folder lab, own machine only:**
1. Purpose: "Learn system audit checks, on my own machine only."
2. Lab place: a folder you create yourself, for lab only, on your own machine.
3. Test data: a test file you create yourself in that folder, with test content only.
4. Network: localhost only. No lab service will listen on all interfaces in this lab.
5. Services: none, except the commands you run yourself in a terminal, on your own machine.
6. Permissions: lab folder 755, test file 644, in lab terms, unless you are practising private file permissions, in which case test file 600, in lab.
7. Plan: "I will check folder permissions and listening ports on my own machine. I will NOT check another machine. I will stop after this audit and write a summary with placeholders only."
8. Reset: delete the test file and recreate it, if a test changes it.
9. Start.

**Common mistakes in building a lab:**
- Building a lab with real personal files in it, because it is "easier" to use existing files. Create test files instead. Easier is not safer.
- Building a lab server that listens on all interfaces, because the default setting did that, and you did not check. Always check `ss -tln` after starting a lab service, on your own machine, and bind it to localhost only for learning labs.
- Building a lab with no plan, no baseline, and no cleanup step. A lab without a plan becomes a mess, and a mess becomes a leak.

---

## 4. Lab rules — the ten rules for every lab session

Write these rules down, and read them before every lab session, until they are habit:

1. **Own lab only.** Test only systems you own, or systems you have written permission to test, in a lab you control.
2. **Test data only.** Test usernames, test passwords as placeholders, sample files you made yourself. No real passwords, no real personal data, no real customer data.
3. **Separate from real life.** Lab folders, lab VMs, and lab accounts are separate from real documents, real accounts, and real personal files. Never mix them.
4. **Localhost or private lab network only, for learning labs.** Do not test on a school, company, public Wi-Fi, or real network, unless that network is your own private lab network and you have written permission for that exact test.
5. **One question, one tool, one check at a time.** Small, slow, and documented beats fast, broad, and undocumented, in lab and in real work.
6. **Read output, write a note, with placeholders only.** A tool output with real details is not a note. A summarised note, with placeholders, is a note you can keep and share safely.
7. **No private file contents.** Check permissions, names, and numbers only. Never read private keys, password files, or personal file contents for a learning check.
8. **Clean up when you are done.** Stop lab services, close tools, delete or secure lab test files as planned, and do not leave a lab server running, a lab folder too open, or a lab report with personal details in a shared folder.
9. **If in doubt, stop and ask.** If you are not sure whether a target is in scope, whether data is test data, or whether a tool is safe to run, stop before you run it, and ask for guidance, with a clear description of what you want to do and why.
10. **A lab finding is a design lesson, not a weapon.** If you find a flaw in your own lab app, write a note about the design mistake, fix it in your own lab, and re-test your fix. Do not use that flaw, or that knowledge, against a real system without written permission.

**Practical design note:**
These ten rules are not "extra work". They are the work. A professional who follows them can be trusted with a real scope, later, because they have practised trust in lab, session after session. A learner who skips them in lab will skip them in real work, too, and that is where harm happens.

---

## 5. Lab networks, simply — localhost, private lab, and real networks are different

**Simple explanation:**
- **Localhost** (`127.0.0.1`, `::1`) = your own machine talking to itself. Traffic never leaves your machine. For learning labs, localhost is the safest network, because there is no other machine involved.
- **Private lab network** = a network you created yourself, for lab only, with lab machines you own, on your own computer or in your own home lab, with no real users on it. Example, in general terms: a VM network, set to host-only or NAT in lab terms, with lab VMs only.
- **Real network** = a school, company, public Wi-Fi, or internet network, with real users, real systems, and real rules. Testing on a real network, without written permission for that exact test, is not a lab, even if you use a lab tool.

**Lab rule, in one sentence:**
For learning in this roadmap, use localhost first, private lab network second, and real networks never, unless you have a clear, written scope for a specific, authorised test, which is a later phase, not a learning lab.

**Common mistakes:**
- Starting a lab server on all interfaces, on a real network, because you forgot to bind it to localhost. Always check `ss -tln` after starting a lab service.
- Using a school or company network for a lab scan, because it is "available". Availability is not permission.
- Thinking a private lab network is safe because it is "private". A private lab network is safe only if it contains only lab machines you own, with test data only, and no real users or real data on it.

---

## 6. Lab data — test data, real data, and secrets are different

**Simple explanation:**
- **Test data** = data you invented for lab, with no real meaning: test usernames, test passwords as placeholders, sample notes, sample files. Test data can be shared in a lab note, with placeholders, safely, because it means nothing outside lab.
- **Real data** = data about a real person, a real account, or a real system. Real data must never be used in a lab, and must never be published in a lab note, screenshot, or public file.
- **Secrets** = passwords, tokens, session IDs, private keys, and codes. Secrets must never be used as test data, never be shared in a note, and never be published, even if they look like test data. A secret is a secret, even in lab.

**Lab rule, in one sentence:**
If you are not sure whether data is test data or real data, treat it as real data, do not use it in lab, and do not share it, until you have confirmed it is test data you created yourself.

**Common mistakes:**
- Using a real password, with a small change, as a "test" password. A small change does not make a real password safe to share. Use a completely invented test word, or a placeholder like `<test-password>`, in notes.
- Copying a real file, with real personal data, into a lab folder, because it is "easier" than creating a test file. Create a test file instead.
- Saving a lab report with a real token, cookie, or session value in it, and then sharing that report. Summarise with placeholders, always.

---

## 7. Cleaning up a lab — the end of every session matters as much as the start

**Algorithm: CLEAN_UP_MY_LAB**

```
Step 1: Stop lab services you started, on your own machine only. Check with ss -tln that they are gone.
Step 2: Close tools: proxies, scanners, and lab apps, in your own lab only.
Step 3: Check lab folders: Are permissions still at the baseline? Are there any extra files you created in lab that you should delete or secure?
Step 4: Check notes: Do your lab notes contain any real username, password, token, cookie, real IP, or real personal data? If yes, remove it immediately, in your private notes, and do not share those notes until they are clean.
Step 5: Decide: Will you keep this lab for next time, or delete it? If you keep it, write down what is in it, in general terms, with placeholders only. If you delete it, delete only lab files you created yourself, never real files.
Step 6: Write one line: "Lab cleaned, services stopped, notes checked, lab kept / deleted." That line is your proof that the session ended safely.
```

**Practical design note:**
A lab that is never cleaned up becomes a real risk over time: services left running, folders left open, reports left with personal details, and test accounts left with weak test passwords that someone might mistake for real accounts. Cleaning up is not tidying — it is security.

**Common mistakes:**
- Leaving a lab web server running overnight, on all interfaces, because you forgot to stop it. Stop it at the end of every session, and check with `ss -tln`.
- Leaving a lab report with real details in a shared folder, because you meant to clean it later. Clean it now, before you share anything.
- Deleting a lab folder that contains real files, because you thought it was only lab files. Check what is in a folder, with `ls`, before you delete it, in lab and in real life.

---

## 8. When a lab goes wrong — what to do, simply

**If a lab test breaks your own lab app:**
1. Stop. Do not run more tests until you understand what broke.
2. Read the error, in your own lab terms, without sharing real details.
3. Reset the lab, using your backup / reset plan from Section 3: recreate test files, or restore a VM snapshot, in your own lab only.
4. Write a note: what broke, what you think caused it, and what you will do differently next time, with placeholders only.
5. Start again, with a smaller test, once, slowly.

**If you realise you tested something outside your lab scope, by mistake:**
1. Stop immediately. Do not run more tests.
2. Do not delete logs or notes to hide it. Write down, privately, what happened, when, and what you tested, in general terms, without real secrets.
3. Tell a trusted person who can help you — a teacher, a mentor, or the owner of the system, if appropriate — and ask what to do next. Hiding a mistake makes it worse.
4. Learn from it: update your lab plan, your checklist, and your scope note, so the same mistake does not happen again.

**If you realise a lab note contains real personal data:**
1. Stop sharing that note immediately.
2. Remove the real data from the note, in your private copy, and do not share the old version.
3. If the note was already shared publicly, ask for help to remove it, and change any real password or token that was in it, immediately, on the real account, not in lab.
4. Write a new note, with placeholders only, and use that note from now on.

**Practical design note:**
Mistakes in lab are learning, if you handle them honestly and safely. Mistakes hidden, or repeated, are not learning — they are risk. The habit of stopping, writing down what happened, and fixing the plan, is the habit that makes a lab safe, over time.

---

## 9. Practice tasks — lab only

1. Build a safe lab, using the BUILD_A_SAFE_LAB algorithm, for a Python password checker with test data only, on your own machine only. Write the purpose, lab place, test data, network rule, services, permissions, plan, and reset plan, in your private notes, with placeholders only.
2. Write the ten lab rules from Section 4, in your own words, without looking back, and check your version against the original. Which rule did you forget, and why does it matter?
3. Run a lab session, with a plan, on your own machine only, for 20 minutes, then run the CLEAN_UP_MY_LAB algorithm, and write the one-line cleanup proof from Section 7.
4. Create a test file in a lab folder, check its permissions, set it to `600` in lab, re-check, and write what changed, with placeholders only.
5. Write a lab note for a system audit you did on your own machine, using the safe summary format from `system-tools.md`, with placeholders only, and check it for any real personal details before you keep it.

---

## 10. Checklist — you are ready to move on when you can do all of these

- [ ] I can explain, in simple words, what a safe lab is, and why separation, test data, and clear boundaries matter.
- [ ] I can explain the three kinds of safe lab, and which one to start with, and why.
- [ ] I can build a safe lab with the BUILD_A_SAFE_LAB algorithm, with purpose, separation, test data, network rule, services, permissions, plan, and reset plan.
- [ ] I can recite, in my own words, the ten lab rules, and I follow them before every lab session.
- [ ] I can explain localhost, private lab network, and real network, and why learning labs use localhost first.
- [ ] I can explain test data, real data, and secrets, and why, if in doubt, I treat data as real data and do not use it in lab.
- [ ] I can clean up a lab with the CLEAN_UP_MY_LAB algorithm, and write a one-line cleanup proof.
- [ ] I know what to do if a lab test breaks my own lab app, if I test outside scope by mistake, or if a lab note contains real personal data — stop, write it down privately, ask for help, and fix the plan.
- [ ] I have not tested, scanned, or probed a real system without written permission, and I have not used real data in a lab.
- [ ] My lab notes, lab folders, and lab reports contain no real username, password, token, cookie, real IP, or real personal data — only test data and placeholders.

**Next phase:**
After safe labs, the roadmap moves to practice notes, books, courses, resources, and checklists — how to practise, what to read, where to learn, and how to check your own progress, in the same big, detailed, simple style, one approved commit at a time, lab only, with generic placeholders only.

---
*All examples in this file are for your own machine or your own private lab only, with test data only. Never publish a real username, password, token, cookie, real IP, or real personal data in a note or public file.*
