# Safe Labs

This is the most important file in this commit, because it is the rulebook for everything after it. A safe lab is a place where you can learn, break things, and fix them, without harming anyone else, without leaking your own data, and without breaking the law or a school or company rule.

If you can build a safe lab, use it with a written plan, clean it up when you're done, and explain why each rule exists in plain words, you are ready for later phases: tools, practice, and real projects, all lab-first.

The one rule above all rules: practise only on systems you own, or systems you have written permission to test, with test data only, in a lab you control. If you don't own it, and you don't have written permission, don't test it, don't scan it, don't probe it, and don't gather information about it for testing. "I only wanted to learn", "I didn't change anything", "It was public", and "Everyone does it" are not permission. Written permission from the owner is permission. When in doubt, stop, and ask: is this my own lab? If the answer is not a clear yes, don't start.

## What a safe lab is

A safe lab is a controlled place, separate from your real life, where you can practise safely. It has three parts: separate machines or folders — a lab machine, a lab virtual machine (VM), or a lab folder, separate from your real documents, real accounts, and real personal files; test data only — test usernames, test passwords as placeholders, sample files you created yourself, and sample websites you built yourself, with no real passwords, no real personal data, no real customer data; and clear boundaries — a written plan for what you will do in lab, what you will not do, and when you will stop and clean up.

A lab that is not separate is not a lab. If your lab files sit with your real documents, your lab passwords match your real passwords, or your lab server is exposed to a real network, a lab mistake can become a real leak. Separation comes before any tool or technique.

## The three kinds of safe lab

**Your own machine, with a lab folder.** A folder on your own machine, used only for lab, with only lab files in it — for example, a folder named `lab-test` in your home folder, on your own machine only. Good for Python labs, password checkers with test data, system audits of your own machine, and simple web servers on localhost only. Its limit is that it shares the same machine as your real life, so you must be careful: lab files only in that folder, lab services only on localhost, and no real personal files in that folder.

**A virtual machine (VM), on your own computer.** A whole computer, simulated inside your real computer, with its own OS, its own files, and its own network settings, separate from your real computer. Good for testing a whole system, installing a deliberately vulnerable lab app, or practising system checks in a place you can reset, without touching your real machine. Its limits are the same rules in a new shape: test data only, lab network only, and snapshots or backups so you can reset it if a lab test breaks it.

**A cloud or rented lab, with your own account, for learning.** A lab environment provided by a learning platform, for a course or a practice room, with its own machines, its own accounts, and its own rules, that you use only within that platform's scope. Good for guided practice rooms, where the platform has already set up a safe target for you, with a clear scope and a clear end point. Its limit is that the platform's scope is the permission: don't go outside that scope, don't test the platform itself, and don't use a learning platform to test a real system outside it.

Start with a lab folder, for Python, password, and system labs. Move to a VM when you need a whole separate system or a deliberately vulnerable lab app. Use a cloud lab only when a course gives you a clear, written scope, and stay inside it. A complex lab with no rules is less safe than a simple lab with clear rules — start simple, and grow only when you can explain why each new part is needed.

## Building a safe lab

Before you start, write the plan down. Purpose, in one sentence — for example, "Learn Python password checking, with test data only." Separation: choose a lab place, separate from real files — a lab folder, or a lab VM, on your own computer only. Test data: create test data only, in that lab place — test usernames, test passwords as placeholders, sample files you made yourself. Network: decide the network rule before you start. For most labs in this roadmap, that is localhost only, or private lab network only, never a real, school, or company network for testing. Services: list the services you will run in lab, and only those — for each, what it is, what port it uses in lab, and whether it listens on localhost only or is exposed, and why. Permissions: set lab folder permissions to a safe baseline before you start — private lab files 600, shared lab folders 755 or stricter, never 777 for a private file. Plan: write a short plan — what you will do, what you will not do, and when you will stop. For example: "I will check my own lab folder permissions. I will not check another machine. I will stop after 30 minutes and clean up." Backup and reset: note how you will reset the lab if a test breaks it — for a folder lab, delete lab files and recreate them; for a VM lab, use a snapshot taken before the test, if your VM tool supports it. Then start, with the plan in front of you. Don't start without a plan.

The building mistakes are predictable: using real personal files because it is "easier" — create test files instead, because easier is not safer. Starting a lab server on all interfaces because the default did that and you didn't check — always check `ss -tln` after starting a lab service, and bind it to localhost only. And building a lab with no plan, no baseline, and no cleanup step — a lab without a plan becomes a mess, and a mess becomes a leak.

## Ten rules for every lab session

Write these down, and read them before every lab session, until they are habit.

Own lab only: test only systems you own, or systems you have written permission to test, in a lab you control. Test data only: test usernames, test passwords as placeholders, sample files you made yourself — no real passwords, no real personal data, no real customer data. Separate from real life: lab folders, lab VMs, and lab accounts are separate from real documents, real accounts, and real personal files — never mix them. Localhost or private lab network only, for learning labs: don't test on a school, company, public Wi-Fi, or real network, unless that network is your own private lab network and you have written permission for that exact test. One question, one tool, one check at a time: small, slow, and documented beats fast, broad, and undocumented, in lab and in real work. Read output, write a note, with placeholders only: a tool output with real details is not a note — a summarised note, with placeholders, is a note you can keep and share safely. No private file contents: check permissions, names, and numbers only, and never read private keys, password files, or personal file contents for a learning check. Clean up when you're done: stop lab services, close tools, delete or secure lab test files as planned, and don't leave a lab server running, a lab folder too open, or a lab report with personal details in a shared folder. If in doubt, stop and ask: if you are not sure whether a target is in scope, whether data is test data, or whether a tool is safe to run, stop before you run it, and ask for guidance, with a clear description of what you want to do and why. And a lab finding is a design lesson, not a weapon: if you find a flaw in your own lab app, write a note about the design mistake, fix it in your own lab, and re-test your fix — don't use that flaw, or that knowledge, against a real system without written permission.

These rules are not "extra work". They are the work — practised trust, session after session, is what earns a real scope later.

## Lab networks: localhost, private lab, and real networks are different

Localhost (`127.0.0.1`, `::1`) is your own machine talking to itself. Traffic never leaves your machine. For learning labs, localhost is the safest network, because there is no other machine involved. A private lab network is a network you created yourself, for lab only, with lab machines you own, on your own computer or in your own home lab, with no real users on it — for example, a VM network set to host-only or NAT in lab terms, with lab VMs only. A real network is a school, company, public Wi-Fi, or internet network, with real users, real systems, and real rules. Testing on a real network, without written permission for that exact test, is not a lab, even if you use a lab tool.

So for learning in this roadmap: use localhost first, private lab network second, and real networks never, unless you have a clear, written scope for a specific, authorised test, which is a later phase, not a learning lab.

The mistakes here are familiar: starting a lab server on all interfaces, on a real network, because you forgot to bind it to localhost — always check `ss -tln` after starting a lab service. Using a school or company network for a lab scan because it is "available" — availability is not permission. And thinking a private lab network is safe because it is "private" — it is safe only if it contains only lab machines you own, with test data only, and no real users or real data on it.

## Lab data: test data, real data, and secrets are different

Test data is data you invented for lab, with no real meaning: test usernames, test passwords as placeholders, sample notes, sample files. Test data can be shared in a lab note, with placeholders, safely, because it means nothing outside lab. Real data is data about a real person, a real account, or a real system. Real data must never be used in a lab, and must never be published in a lab note, screenshot, or public file. Secrets are passwords, tokens, session IDs, private keys, and codes. Secrets must never be used as test data, never be shared in a note, and never be published, even if they look like test data. A secret is a secret, even in lab.

The rule, in one sentence: if you are not sure whether data is test data or real data, treat it as real data, don't use it in lab, and don't share it, until you have confirmed it is test data you created yourself.

That means don't use a real password, with a small change, as a "test" password — a small change does not make a real password safe to share. Use a completely invented test word, or a placeholder like `<test-password>`, in notes. Don't copy a real file, with real personal data, into a lab folder because it is "easier" than creating a test file — create a test file instead. And don't save a lab report with a real token, cookie, or session value in it and then share that report. Summarise with placeholders, always.

## Cleaning up: the end of every session matters as much as the start

When you're done, stop the lab services you started, on your own machine only, and check with `ss -tln` that they are gone. Close your tools: proxies, scanners, and lab apps, in your own lab only. Check your lab folders: are permissions still at the baseline, and are there any extra files you created in lab that you should delete or secure? Check your notes: do they contain any real username, password, token, cookie, real IP, or real personal data? If yes, remove it immediately, in your private notes, and don't share those notes until they are clean. Then decide: will you keep this lab for next time, or delete it? If you keep it, write down what is in it, in general terms, with placeholders only. If you delete it, delete only lab files you created yourself, never real files. Finish with one line: "Lab cleaned, services stopped, notes checked, lab kept / deleted." That line is your proof that the session ended safely.

A lab that is never cleaned up becomes a real risk over time: services left running, folders left open, reports left with personal details. Cleaning up is not tidying — it is security. Don't leave a lab server running overnight on all interfaces, or a report with real details in a shared folder because you meant to clean it later. And check a folder with `ls` before you delete it — don't delete real files you mistook for lab files.

## When a lab goes wrong

If a lab test breaks your own lab app, stop. Don't run more tests until you understand what broke. Read the error, in your own lab terms, without sharing real details. Reset the lab using your backup and reset plan: recreate test files, or restore a VM snapshot, in your own lab only. Write a note — what broke, what you think caused it, and what you will do differently next time, with placeholders only. Then start again, with a smaller test, once, slowly.

If you realise you tested something outside your lab scope, by mistake, stop immediately. Don't run more tests. Don't delete logs or notes to hide it. Write down, privately, what happened, when, and what you tested, in general terms, without real secrets. Tell a trusted person who can help you — a teacher, a mentor, or the owner of the system, if appropriate — and ask what to do next. Hiding a mistake makes it worse. Then learn from it: update your lab plan and your scope note, so the same mistake does not happen again.

If you realise a lab note contains real personal data, stop sharing that note immediately. Remove the real data from the note, in your private copy, and don't share the old version. If the note was already shared publicly, ask for help to remove it, and change any real password or token that was in it, immediately, on the real account, not in lab. Write a new note, with placeholders only, and use that note from now on.

Mistakes handled honestly are learning. Mistakes hidden or repeated are risk.

---
*All examples here are for your own machine or your own private lab only, with test data only. Never publish a real username, password, token, cookie, real IP, or real personal data in a note or public file.*
