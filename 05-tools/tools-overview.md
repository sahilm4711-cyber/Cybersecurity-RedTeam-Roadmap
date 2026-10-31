# Tools Overview

You already know computers, Linux, networks, and the web. Now you meet the tools — not as a list to memorise, but as a way of asking better questions.

A working red-teamer does not start with a tool. They start with a question, then pick the smallest tool that answers it. If you can say, for any tool, what question it answers, what you feed it, what it spits out, and what that output does *not* prove, you understand it. If you only know its name, you don't.

Every example here is for your own machine or your own private lab only. A tool never gives you permission. Permission comes from the owner, in writing, before you start. In learning, your own lab is your scope. Use test data only, and generic placeholders only: `your-user`, `<your-lab-ip>`, `<test-password>`, `<lab-port>`, `<lab-domain>`.

## A tool is an answer to a question

Every tool follows the same shape: question, input, action, output, meaning, next question. Get in the habit of writing the question down in one sentence before you run anything.

```text
Question: What is listening on my own machine?
Tool: ss
Command: ss -tln
Meaning: LISTEN means a service is waiting. 127.0.0.1 means
  localhost only. 0.0.0.0 means it is exposed to the network.
Limit: this tells you about your own machine, nothing about a remote host.
```

That short note is worth more than a page of pasted output. If you cannot write one after running a tool, you have only run it — you haven't learned it.

Watch out for the most common beginner trap: reaching for the biggest, most famous tool first. It floods you with output, teaches you nothing, and on a real system it would be unauthorised testing. Start small, start with a question, run once, read line by line.

## The five families

**System and network checks.** These answer: what is on my own machine, what is listening, what is my network? You already know them: `ip`, `ss`, `ping`, `dig`, `curl`, `ps`, `ls`. Use them first, every time. They are local, mostly read-only, and they settle half your questions before you need anything fancier.

**Recon — information gathering, lab only.** What is known about a lab target I own? Names, addresses, services in my lab. DNS tools, the WHOIS idea, subdomain and service listing in lab — detail is in `recon-tools.md`. Recon on a real domain, company, or person without permission is not learning. It is probing, and it shows up in logs.

**Scanning — what is open, lab only.** Which ports are open on a lab host I own, and which services sit behind them? A port scanner in lab, against one VM you own, run slowly, after you have written down why. Never scan a host you don't own. A scan is noisy, it is logged, and it is a clear sign of testing.

**Web tools — checking a lab website.** How does this lab site work? What requests does it make, what does it return, where is the design weak? Browser developer tools, `curl`, a web proxy in lab, a web scanner in lab — detail is in `web-tools.md`. Only against a site you built yourself, or a deliberately vulnerable lab app on your own machine. Never against a real site.

**Password, system, and reporting tools.** Is this lab password weak? Is this lab system configured safely? How do I write a report that is actually useful? Password checkers with test passwords only, system audits on your own machine only, and report templates. Those get their own files later in this phase.

A professional does not collect tools. They collect questions, and they know which tool answers which question, and where that tool stops being useful.

## Learning a new tool, properly

Read the tool's own help first — `tool --help`, or `man tool`, on your own machine. Write down, in one sentence, what question it answers. Find one safe, lab-only example in this roadmap or in the tool's own docs. Run that one example, once, on your own machine or lab VM.

Then read the output line by line. Mark three lines you understand and one you don't, and look up only the one you don't. Run it again and explain the output in your own words, without looking. Finish with a short note: tool name, question, command, what the output means, its limits, and one pitfall to avoid. Don't move on to a new target until that note exists. It takes five minutes and it is the difference between someone who has run a tool and someone who can use it.

## Output: what it proves, and what it doesn't

This is the skill that separates a tester from a tool-runner. Every output has limits, and a good report states them plainly.

A `ping` reply proves ICMP came back from that lab host. It does not prove a web service is running or a port is open. `ss -tln` showing LISTEN proves a service is listening on your own machine, on that port. It says nothing about a remote host. A `dig` answer proves DNS gave that answer, at that time. It does not prove a site is up or secure. `curl -i` returning 200 proves that lab service returned 200 for that one request. It does not prove the whole site is secure. An open port in a scan proves that lab port responded, in that scan. It does not prove the service is vulnerable — or safe. A scanner finding proves the lab app behaved in a certain way, in that test. It does not prove a real app has the same flaw, or that the finding is exploitable.

So never write "Tool X found Y, therefore the system is hacked." Write: "Tool X, with this lab input, gave this output, which means this, and does not mean that, and the next check needed is this." That sentence structure is the whole of red-team reporting, in miniature.

## Before every run

Run this through your head before you press Enter. Is the target my own machine or my own private lab, only? Do I have the question written down? Is this the smallest tool that answers it? Is the input a lab IP, lab domain, lab file, or lab URL only? Am I using test data only, with no real password, token, or personal data? Will I read the output line by line and write a note with placeholders only? Will I stop lab services and close the tool when I'm done? If any answer is no, fix it first. That habit, repeated, is what separates a learner in a lab from someone who causes harm by accident.

A few more things I have learned the hard way: never copy a full tool output with real IPs, hostnames, or secrets into a public note or screenshot — summarise with placeholders. Never trust one tool alone — cross-check with a second, smaller tool and with your own understanding of the system. And never run a sweep or flood in lab just because a tool allows it. Allowed by the tool is not the same as needed for learning. One host, one check, slowly.

Next: `recon-tools.md`, then `web-tools.md`. For each new tool there, write a tool note before you run anything.

---
*All examples here are for your own machine or your own private lab only. Use test data only. Never publish a real username, hostname, password, token, or real IP in a note or public file.*
