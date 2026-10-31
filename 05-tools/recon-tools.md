# 05 — Tools: Recon Tools, Fully Explained

## What this file is

This is Phase 4, part 2. Recon, short for reconnaissance, is information gathering. Before you test anything, you first understand what exists: what names, what addresses, what services, in your lab.

Recon is not hacking. Recon is asking, in a careful, limited way: What is here, in my own lab, that I already own? The skill is in asking small, precise questions, writing down what you learn, and stopping before you probe anything you do not own.

No shortcuts here. Each recon idea gets: simple explanation, what, why, when, when NOT to use it, lab example on your own machine only, output meaning, and common mistakes.

**Safety rule for this whole file:**
Recon in this file is for your own lab only.
- Use lab domains you created yourself, lab VMs you own, and lab files you made yourself.
- Never gather information about a real person, company, domain, or system without written permission. Searching public information about a real target, enumerating its subdomains, or listing its services, without permission, is probing, not learning.
- Use generic placeholders only in notes: `<lab-domain>`, `<your-lab-ip>`, `your-user`, `<test-password>`.
- Never put a real domain, real IP, real username, or real personal data in a note, screenshot, or public file.

---

## 1. Recon, in one picture

Think of recon as making a map before a journey, using only your own map:

- **What exists?** Names, IPs, services, in my lab.
- **What is it for?** Each name or service has a role in my lab.
- **What is already known?** What did I write down last time, so I do not repeat a check?
- **What is still unknown?** What is the next small question?

**Practical design note:**
Good recon is boring, in a good way: small checks, clear notes, no surprises. Bad recon is a flood of queries, a sweep of many hosts, and a pile of output with no question behind it. In a real engagement, later, recon is also the part that is most visible in logs — so a professional keeps it minimal, authorised, and documented. In learning, your lab is your authorised scope, and your notes are your documentation.

**Algorithm: RECON_MY_LAB**

```
Step 1: Write the lab inventory you already know: lab hosts, lab domains, lab services.
Step 2: Write one recon question, e.g. "What DNS records does my lab domain have?"
Step 3: Choose the smallest tool that answers that question.
Step 4: Run it once, with a lab-only input.
Step 5: Read the output line by line, write the meaning in your own words.
Step 6: Update your lab inventory with what you learned, using placeholders in public notes.
Step 7: Ask the next question, or stop. Do not sweep, enumerate, or flood.
```

---

## 2. DNS recon — what does DNS say about my lab name?

**What:**
DNS recon asks DNS, one name at a time, for records about a lab domain you own. You already met `dig` in network tools. Here, you use it as a recon tool, with a purpose.

**Why:**
A domain name is often the first thing you know about a system. DNS tells you: What IP does this name point to? What mail servers exist for this lab domain? What other names point to the same place, in lab?

**When:**
When you have just created a lab domain, or when a lab service does not respond and you want to check if DNS is the problem first, before checking the service itself.

**When NOT to use it:**
Do not query many names, do not try to list all subdomains of a real domain, do not query for private internal names you do not own. One lab name, one query, is enough for learning.

**Lab example — one query only, lab domain only:**

```bash
dig <lab-domain> +short
dig <lab-domain> A
dig <lab-domain> MX
```

Replace `<lab-domain>` with a lab name you created yourself, in your own lab DNS or hosts file. If you do not have a lab domain yet, use `example.com` as a public learning example only, for one query, not as a target.

**Output meaning:**
- `A` record = IPv4 address for that name, in lab.
- `AAAA` record = IPv6 address for that name, in lab.
- `MX` record = mail server for that lab domain, if you set one up in lab.
- `CNAME` record = alias, one lab name points to another lab name.
- `TXT` record = text, often used for verification in lab.
- `NXDOMAIN` = that name does not exist in DNS. It does not mean a host is down, it means DNS has no record for it.
- `NOERROR` with no answer = DNS worked, but there is no record of that type for that name.

**Common mistakes:**
- Thinking a DNS answer proves a service is running. DNS only gives an address. The service can still be down.
- Enumerating subdomains of a real domain to "find hidden things". That is not a single learning check, it is enumeration, and it needs written permission.
- Sharing a real DNS output with real internal names publicly. Summarise with placeholders.

**Practice task:**
Run one `dig` query for a lab name you own, or for `example.com` as a learning example. Write down the record type and the answer, in your own words, with placeholders if it is a lab name.

---

## 3. WHOIS and registration, concept only

**What:**
WHOIS is a public record system for domain registrations: who registered a domain, when, and which nameservers it uses. In the past, it often showed personal contact details. Today, most records are redacted for privacy, and many domains use privacy protection.

**Why, in simple terms:**
For a lab domain you own, WHOIS tells you: When did I register this lab domain? When does it expire? Which nameservers does it use? That is useful for managing your own lab, not for investigating other people.

**When NOT to use it:**
Do not use WHOIS to gather personal information about a real person or company, do not use it to find contact details for social engineering, and do not publish WHOIS data about anyone else. Privacy rules and the purpose of the query matter, even if the data is technically public.

**Lab example — concept only, no command to run on a real domain:**
For your own lab domain, check your own registrar dashboard, on your own account, for: registration date, expiry date, and nameservers. Write those down in your private lab notes, not in a public file. That is the whole lab task for WHOIS — managing your own domain, not investigating others.

**Common mistake:**
Thinking "public" means "free to use for any purpose". Public records still have a purpose, and using them to target a person is not learning.

---

## 4. Service identification in lab — what is running on my lab host?

**What:**
Service identification asks, for a lab host you own: Which ports are open, and which services are behind them? You already know `ss` for your own machine. For a lab VM you own, the same question applies, but you must be careful: checking a remote host, even in lab, is a scan, not a local check.

**Why:**
You cannot test or defend a service you cannot name. "Port 80 is open" is not enough. "Port 80 is a web service, on my lab VM, serving my lab page" is a useful identification.

**When:**
Only for a lab VM you own, after you have written down why you need to check it, and only with a small, slow check, one host at a time.

**When NOT to use it:**
Never scan a host you do not own, never scan a range of hosts, never scan quickly or repeatedly, and never use a scan result as proof that a service is vulnerable. A scan only says a port responded.

**Lab concept — no scan command to run on a real host:**
1. Write down the lab host you own, and why you are checking it.
2. On that lab host itself, run `ss -tln` first. That is the safest, local check, and it often answers the question without any scan at all.
3. If you still need a remote check in lab, use a single, slow, lab-only scan against that one lab VM, and stop after one run.
4. For each open port in lab, write: port, service name (from your lab setup, not from a guess), and whether it should be open in lab.
5. Close lab services you do not need, and re-check with `ss` on the lab host itself.

**Output meaning, lab only:**
- Open = that lab port responded, in that scan, at that time.
- Closed = that lab port did not respond with a service, in that scan.
- Filtered = a firewall in lab dropped the probe, so you got no reply. Filtered does not mean closed, and it does not mean a service is hidden for security — it means the firewall dropped it.
- Service name from a scan is a guess based on the port number and banner, if any. Confirm it from your lab setup, not from the scan alone.

**Common mistakes:**
- Scanning a whole subnet "to see what is there" in lab, instead of checking your lab inventory list. Your inventory list is faster, quieter, and more accurate.
- Thinking an open port means a vulnerability. An open port is a door. Whether it is a problem depends on what service is behind it, who should reach it, and how it is configured — in lab, with your own setup.
- Sharing a scan output with real IPs and hostnames publicly. Summarise with placeholders.

---

## 5. Subdomain and directory listing, lab only, concept only

**What:**
In a lab website you built yourself, you may have subdomains (like `lab.<lab-domain>`) or directories (like `/lab-notes/`) that you want to inventory. Listing them, in lab, is a way to check: Did I leave a test page, a backup file, or a sample directory enabled that I forgot about?

**Why:**
Forgotten test pages and backup files are a classic misconfiguration (see OWASP A05). In your own lab, listing your own directories is a way to clean up your own app, not to find hidden things on someone else's app.

**When NOT to use it:**
Do not enumerate subdomains or directories of a real website, do not try common wordlists against a real site, and do not download files you find that way. That is unauthorised testing, even if the file was "publicly accessible" by a guessed URL.

**Lab example — own lab website only:**
1. List, from your own lab project folder, the pages and directories you actually created. That list is your inventory.
2. Compare that list to what your lab web server actually serves. Are there any extra pages, sample pages, or backup files you did not intend to serve?
3. Remove or disable the extras in your own lab, and re-check.
4. Write a note: what you removed, and why, with placeholders only.

That inventory-first method teaches the same lesson as enumeration, without probing a real target.

**Common mistake:**
Thinking "if I can guess the URL, it is fair game". Guessing is still accessing a system in a way the owner did not intend, and in real life it needs written permission first.

---

## 6. OSINT, simply — what is already public, and why it needs care

**What:**
OSINT (Open-Source Intelligence) is information gathered from public sources: public websites, public documents, public social media, public code, and so on. It is a large field, and it is easy to misuse.

**Why it matters for a learner:**
In lab, OSINT means: reading public documentation for a tool you use, reading public guidance from OWASP, and learning from public lab write-ups written by people who tested their own labs. That kind of OSINT is safe, useful, and encouraged.

**When NOT to use it:**
Do not gather OSINT about a real person, employee, or company to target them, do not collect personal data (addresses, phone numbers, family details, etc.), and do not use OSINT to plan social engineering. That is not learning, and it can cause real harm, even if the data was "public".

**Lab practice, safe OSINT only:**
- Read the official documentation for a tool you use in lab, and write a short note about one feature you learned.
- Read one public lab write-up about a lab system (not a real system), and write down what design flaw it explained, in your own words.
- Do not write a report about a real person or company. Write about a lab system, a tool, or a concept.

**Common mistake:**
Thinking OSINT is "just Googling, so it is harmless". Gathering, combining, and acting on personal information about a real person is not harmless, even if each piece was public on its own.

---

## 7. Recon notes: how to write them so they help you later

**A good recon note, lab only, looks like this:**

```
Lab: my own lab network, date: <date>
Question: What DNS records does my lab domain have?
Tool: dig, one query
Input: <lab-domain>
Output meaning: A record points to <your-lab-ip>, MX record points to <lab-mail-host>
Inventory update: lab domain -> lab IP, lab mail host
Next question: Is the lab web service listening on that lab host?
Personal data: none, placeholders only
```

**What a bad recon note looks like:**
A long, unedited tool output, with real IPs, real hostnames, and no question, no meaning, and no next step. That note helps no one, and it is a leak if it is shared.

**Algorithm: WRITE_A_RECON_NOTE**

```
Step 1: Question, in one sentence.
Step 2: Tool and command, lab only.
Step 3: Output, summarised, not copied in full.
Step 4: Meaning, in your own words.
Step 5: Inventory update, with placeholders in public notes.
Step 6: Next question, or "stop".
```

---

## 8. Common mistakes across recon

- Recon without a question, without a scope, and without notes. That is noise, not recon.
- Recon on a real target, because "it is only public information" or "I did not change anything". Gathering is still testing, and it still needs permission.
- Sweeping, enumerating, or flooding, in lab or elsewhere, because a tool allows it. One host, one name, one query, slowly.
- Sharing recon output publicly, with real names, IPs, or personal data. Summarise with placeholders, always.
- Thinking recon is separate from ethics. Recon is the first step of any test, so it is where ethics starts, not where it ends.

---

## 9. Practice tasks — lab only

1. Write a lab inventory for your own lab: hosts, domains, services, with placeholders in any public note.
2. Run one `dig` query for a lab name you own, or for `example.com` as a learning example, and write a recon note for it.
3. On your own machine, run `ss -tln` and write a service identification note: which ports are listening, localhost only or exposed, and what lab service each one is.
4. List, from your own lab project folder, the pages you actually created for a lab website, and compare it to what your lab server serves. Remove any extra sample page in your own lab.
5. Read one piece of public tool documentation, and write a short note about one feature you learned, in your own words. Do not write about a real person or company.

---

## 10. Checklist — you are ready to move on when you can do all of these

- [ ] I can explain, in simple words, what recon is, and what question it answers.
- [ ] I can run the RECON_MY_LAB algorithm, with one question at a time, lab only.
- [ ] I can use `dig` for one lab name, and explain A, AAAA, MX, CNAME, TXT, and NXDOMAIN.
- [ ] I can explain WHOIS as a management tool for my own lab domain, not as an investigation tool for others.
- [ ] I can identify services on my own machine with `ss`, and on a lab VM I own, with a small, slow, lab-only check, after writing down why.
- [ ] I can inventory my own lab website's pages from my own project folder, without enumerating a real site.
- [ ] I can explain OSINT safely: public documentation and lab write-ups, not personal data about real people.
- [ ] I can write a recon note with question, tool, summarised output, meaning, inventory update, and next question, using placeholders only.
- [ ] I have not gathered information about a real person, company, domain, or system without written permission.
- [ ] My notes contain no real domain, real IP, real username, or real personal data — only generic placeholders.

**Next file:**
After this, read `web-tools.md` in the same folder. It takes the same question-first, lab-only method and applies it to web testing tools, for a lab website you built yourself.

---
*All examples in this file are for your own machine or your own private lab only. Use test data only. Never publish a real domain, real IP, real username, or real personal data in a note or public file.*
