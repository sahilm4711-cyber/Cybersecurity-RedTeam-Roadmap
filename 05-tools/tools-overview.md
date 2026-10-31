# 05 — Tools: Tools Overview, Fully Explained

## What this file is

This is Phase 4, part 1. You already know computers, Linux, networks, and the web. Now you learn the tools — not as a list to memorise, but as a system: what kind of job each tool does, when to use it, and when NOT to use it.

If you can, for any tool, answer in simple English: What question does it answer? What input does it need? What output does it give? What does that output prove, and what does it NOT prove? — then you understand tools. If you only know tool names, you do not.

No shortcuts here. Each tool class gets: simple explanation, what, why, when, when NOT to use it, lab example on your own machine only, output meaning, and common mistakes.

**Safety rule for this whole file:**
All tools here are for your own machine, or your own private lab, only, with written permission for any other system you ever test in the future.
- Never run a scanner, enumerator, or tester against a real website, network, or computer you do not own or do not have written permission to test.
- Use test data only, and generic placeholders only: `your-user`, `<your-lab-ip>`, `<test-password>`, `<lab-port>`, `<lab-domain>`.
- Never put a real username, hostname, password, token, or real IP in a note, screenshot, or public file.
- A tool does not give you permission. Permission comes from the owner, in writing, before you start. In learning, your own lab is your permission.

---

## 1. Tools are answers to questions, not magic

**Practical design note:**
Every tool fits this pattern:
1. Question: What do I want to know?
2. Input: What do I give the tool? A lab IP, a lab domain, a lab file, a lab URL?
3. Action: What does the tool do with that input, in simple terms?
4. Output: What does it show?
5. Meaning: What does that output prove?
6. Next question: What is still unknown?

**Algorithm: CHOOSE_A_TOOL_SAFELY**

```
Step 1: Write the question in one sentence.
Step 2: Write the scope: my own machine, or my own private lab, only?
Step 3: If the scope is not my own lab, STOP. Do not run the tool.
Step 4: Choose the smallest tool that answers that question.
Step 5: Run it once, with a small, lab-only input.
Step 6: Read the output line by line. Do not guess.
Step 7: Write down: question, command, output, meaning, next question.
Step 8: Clean up: stop lab services, close the tool, do not publish real data.
```

**Common mistake:**
Running the biggest, most famous tool first, without a question. That gives you a flood of output, no understanding, and, on a real system, unauthorised testing. Start small, start with a question.

---

## 2. The five tool families, in simple words

### Family 1: System and network check tools
What they answer: What is on my own machine? What is listening? What is my network?
Examples you already know: `ip`, `ss`, `ping`, `dig`, `curl`, `ps`, `ls`.
Use them first, every time. They are safe, read-only, and local.

### Family 2: Recon tools — information gathering, lab only
What they answer: What is publicly known about a lab target I own? What names, addresses, and services exist in my lab?
Examples, concept only in this file, detail in `recon-tools.md`: DNS tools, WHOIS concept, subdomain listing in lab, service identification in lab.
Rule: Recon on your own lab only. Recon on a real domain, company, or person without permission is not learning — it is probing.

### Family 3: Scanning tools — what is open, lab only
What they answer: Which ports are open on a lab host I own? Which services are running there?
Example, concept only: a port scanner in lab, against a lab VM you own, with a small, slow scan.
Rule: Never scan a host you do not own. A scan is noisy, it is logged, and it is a clear sign of testing. In lab, scan one host at a time, slowly, and only after you have written down why.

### Family 4: Web tools — checking a lab website
What they answer: How does this lab website work? What requests does it make? What does it return? What design flaws does it have, in lab?
Examples, detail in `web-tools.md`: browser developer tools, `curl`, a web proxy in lab, a web scanner in lab.
Rule: Only against a lab website you built yourself, or a deliberately vulnerable lab app on your own machine. Never against a real website.

### Family 5: Password, system, and reporting tools — later phases
What they answer: Is this lab password weak? Is this lab system configured safely? How do I write a clear report?
Examples, later commits: password strength checkers with test passwords only, system audit tools on your own machine only, and report templates.
Rule: Test passwords only, own machine only, and a report must never contain real secrets, real IPs, or real personal data.

**Design note:**
A professional does not collect tools. A professional collects questions, and knows which tool answers which question, with what limits. Your roadmap, in later phases, will return to each family with labs, one family at a time.

---

## 3. How to learn a new tool, step by step

**Algorithm: LEARN_A_TOOL**

```
Step 1: Read the tool's own help, on your own machine: tool --help, or man tool, in lab.
Step 2: Write, in one sentence, what question it answers.
Step 3: Find one safe, lab-only example in this roadmap or in the tool's own documentation.
Step 4: Run that one example, once, on your own machine or lab VM.
Step 5: Read the output line by line. Underline three lines you understand, and one line you do not.
Step 6: Look up only the line you do not understand, in the tool's documentation.
Step 7: Run the example again, and explain the output in your own words, without looking.
Step 8: Write a short note: tool name, question, command, output meaning, limits, mistakes to avoid.
Step 9: Stop. Do not run the tool against a new target until you have written that note.
```

**What a good tool note looks like, lab only:**

```
Tool: ss
Question: What is listening on my own machine?
Command: ss -tln
Output meaning: LISTEN = waiting, 127.0.0.1 = localhost only, 0.0.0.0 = exposed
Limits: Shows my own machine only, not remote hosts
Mistakes: Thinking LISTEN means attacked; sharing full output publicly
```

If you cannot write that note, you have not learned the tool yet — you have only run it.

---

## 4. Output: what it proves, and what it does not prove

This is the most important skill in this file. Every tool output has limits.

| Tool output | It proves | It does NOT prove |
|---|---|---|
| `ping` replies | ICMP replies came back from that lab host | That a web service is running, or a port is open |
| `ss -tln` LISTEN | A service is listening on my own machine, on that port | That a remote host has that port open |
| `dig` answer | DNS gave that answer, at that time | That a website is up, or secure |
| `curl -i` 200 | That lab service returned 200 for that request | That the whole site is secure |
| Port scan open | That lab port responded, in that scan | That the service is vulnerable, or safe |
| Web scanner finding | That lab app behaved in a certain way, in that test | That a real app has the same flaw, or that the finding is exploitable |

**Practical design note:**
A report that says "Tool X found Y, therefore the system is hacked" is a bad report. A good report says: "Tool X, with this lab input, gave this output, which means this, and does not mean that, and the next check needed is this." That is the style you are building for red-team reporting, later.

---

## 5. Tool safety checklist, before every run

Run through this checklist, in your head, before you press Enter:

- [ ] Is the target my own machine, or my own private lab, only?
- [ ] Do I have the question written down, in one sentence?
- [ ] Is this the smallest tool that answers that question?
- [ ] Is the input a lab IP, lab domain, lab file, or lab URL only?
- [ ] Am I using test data only, with no real password, token, or personal data?
- [ ] Will I read the output line by line, and write a note with placeholders only?
- [ ] Will I stop lab services and close the tool when I am done?
- [ ] If anything here is "no", will I stop and fix it before running?

If you cannot tick every box, do not run the tool yet. Fix the missing box first. That habit, repeated, is what separates a learner in a lab from someone who causes harm by accident.

---

## 6. Common mistakes across all tools

- Running a tool because it is famous, not because it answers your question.
- Running a tool against a real target "just to see", "just once", or "without changing anything". Seeing is still testing, and it is still logged.
- Copying a full tool output with real IPs, hostnames, or secrets into a public note, screenshot, or repo. Summarise with placeholders.
- Trusting one tool alone. Cross-check with a second, smaller tool, and with your own understanding of the system.
- Running a scan, sweep, or flood in lab, because a tool allows it. Allowed by the tool is not the same as safe, or needed for learning. One host, one check, slowly.
- Forgetting to update tools, or using an old tool version with known flaws, in lab. Keep a simple list of tool versions for lab projects, and update in lab first, one at a time.
- Thinking a tool result is a verdict. It is evidence for one question, at one time, in one lab.

---

## 7. Practice tasks — lab only

1. Pick one tool you already know (`ss`, `dig`, or `curl`). Write a full tool note for it, using the format in Section 3, with placeholders only.
2. Pick a question you do not yet know the answer to, about your own machine only. Choose the smallest tool that answers it, run it once, and write the output meaning in your own words.
3. Take a tool output you have seen before (in this roadmap, or in your own lab). Write two sentences: what it proves, and what it does NOT prove.
4. Run the safety checklist in Section 5, out loud, before your next lab tool run. Write down any box you could not tick, and how you fixed it.
5. Read `recon-tools.md` next, then `web-tools.md`. For each new tool there, write a tool note before you run anything.

---

## 8. Checklist — you are ready to move on when you can do all of these

- [ ] I can explain, in simple words, the five tool families and what question each answers.
- [ ] I can run the CHOOSE_A_TOOL_SAFELY algorithm before any tool run.
- [ ] I can learn a new tool with the LEARN_A_TOOL algorithm, and write a short tool note for it.
- [ ] I can explain, for any tool output, what it proves and what it does NOT prove.
- [ ] I can run the safety checklist before every run, and I stop if any box is "no".
- [ ] I have not run any scanner, enumerator, or tester against a real system without written permission.
- [ ] My notes contain no real username, hostname, password, token, or real IP — only generic placeholders.

**Next files:**
After this, read `recon-tools.md` for information gathering in lab, then `web-tools.md` for web testing in lab. Both use the same question-first, lab-only, note-first method you just learned.

---
*All examples in this file are for your own machine or your own private lab only. Use test data only. Never publish a real username, hostname, password, token, or real IP in a note or public file.*
