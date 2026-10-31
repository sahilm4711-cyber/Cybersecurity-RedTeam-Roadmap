# Recon Tools

Recon — reconnaissance — is information gathering. Before you test anything, you map what exists: what names, what addresses, what services, in your lab.

Recon is not hacking. It is asking, in a careful, limited way: what is here, in my own lab, that I already own? The skill is asking small, precise questions, writing down what you learn, and stopping before you probe anything you don't own.

This whole file is lab only. Use lab domains you created yourself, lab VMs you own, and lab files you made yourself. Never gather information about a real person, company, domain, or system without written permission. Searching public information about a real target, enumerating its subdomains, or listing its services without permission is probing, not learning. In notes, use placeholders only: `<lab-domain>`, `<your-lab-ip>`, `your-user`, `<test-password>`.

## Think of it as making a map

Good recon is boring in a good way: small checks, clear notes, no surprises. Bad recon is a flood of queries, a sweep of many hosts, and a pile of output with no question behind it.

In real engagements, recon is also the most visible part — it shows up in logs. So a professional keeps it minimal, authorised, and documented. In learning, your lab is your authorised scope, and your notes are your documentation.

A sensible working rhythm looks like this: write down the lab inventory you already know, pick one question, pick the smallest tool that answers it, run it once with a lab-only input, read the output line by line, update your inventory, and then either ask the next question or stop. Don't sweep, enumerate, or flood. One host, one name, one query, slowly.

A good recon note is short:

```text
Lab: my own lab network, date: <date>
Question: What DNS records does my lab domain have?
Tool: dig, one query
Input: <lab-domain>
Meaning: A record points to <your-lab-ip>,
  MX record points to <lab-mail-host>
Inventory update: lab domain -> lab IP, lab mail host
Next question: Is the lab web service listening on that lab host?
Personal data: none, placeholders only
```

A bad one is a long, unedited tool output with real IPs and hostnames, no question, no meaning, and no next step. That note helps nobody, and if it is shared, it leaks.

## DNS recon: what does DNS say about my lab name?

DNS recon asks DNS, one name at a time, for records about a lab domain you own. You already met `dig` in network tools. Here you use it with a purpose.

A domain name is often the first thing you know about a system. DNS tells you what IP a name points to, what mail servers exist for that lab domain, and what other names point to the same place in lab. It is the right first check when you have just created a lab domain, or when a lab service doesn't respond and you want to rule out DNS before you blame the service itself.

```bash
dig <lab-domain> +short
dig <lab-domain> A
dig <lab-domain> MX
```

Replace `<lab-domain>` with a lab name you created yourself, in your own lab DNS or hosts file. If you don't have a lab domain yet, `example.com` works as a public learning example for one query — not as a target.

Read the answer for what it is. An A record is an IPv4 address for that name, in lab. AAAA is IPv6. MX is the mail server for that lab domain, if you set one up. CNAME means one lab name is an alias for another. TXT is text, often used for verification in lab. NXDOMAIN means DNS has no record for that name — it does not mean a host is down. NOERROR with no answer means DNS worked, but there is no record of that type for that name.

The trap here is over-reading. A DNS answer does not prove a service is running. DNS only gives an address. The service can still be down. And enumerating subdomains of a real domain to "find hidden things" is not a single learning check — it is enumeration, and it needs written permission.

## WHOIS and registration: a management tool, not an investigation tool

WHOIS is the public record system for domain registrations: who registered a domain, when, and which nameservers it uses. In the past it often showed personal contact details. Today most records are redacted, and many domains use privacy protection.

For a lab domain you own, WHOIS answers a boring, useful question: when did I register this, when does it expire, which nameservers does it use? That is for managing your own lab, not for investigating other people.

There is no command to run here on a real domain. For your own lab domain, check your own registrar dashboard, on your own account, for registration date, expiry, and nameservers, and keep that in your private lab notes, not a public file.

"Public" does not mean "free to use for any purpose." Public records still have a purpose, and using them to target a person is not learning.

## Service identification in lab: what is running on my lab host?

This asks, for a lab host you own: which ports are open, and which services are behind them? You already know `ss` for your own machine. For a lab VM you own, the same question applies — but checking a remote host, even in lab, is a scan, not a local check, so treat it with more care.

Start on the lab host itself. Run `ss -tln` there first. That is the safest, local check, and it often answers the question without any scan at all. Only if you still need a remote check in lab, run a single, slow, lab-only scan against that one lab VM, and stop after one run. Then, for each open port, write the port, the service name from your lab setup (not from a guess), and whether it should be open in lab. Close anything you don't need, and re-check with `ss` on the host itself.

Read scan output carefully. Open means that lab port responded, in that scan, at that time. Closed means it did not respond with a service, in that scan. Filtered means a firewall in lab dropped the probe, so you got no reply — filtered does not mean closed, and it does not mean a service is hidden for security. It means the firewall dropped it. And a service name from a scan is a guess based on the port number and banner, if any. Confirm it from your lab setup, not from the scan alone.

The two errors I see most: scanning a whole subnet "to see what is there" instead of checking your inventory list — your list is faster, quieter, and more accurate — and thinking an open port means a vulnerability. An open port is a door. Whether it is a problem depends on what service is behind it, who should reach it, and how it is configured, in lab, with your own setup.

## Subdomains and directories in lab: inventory, don't enumerate

In a lab website you built yourself, you may have subdomains like `lab.<lab-domain>` or directories like `/lab-notes/` that you want to account for. The lab version of this check is not enumeration — it is inventory.

List, from your own lab project folder, the pages and directories you actually created. Compare that list to what your lab web server actually serves. Are there extra pages, sample pages, or backup files you didn't intend to serve? Remove or disable the extras in your own lab, and re-check. Write a note about what you removed and why, with placeholders only.

That inventory-first method teaches the same lesson as enumeration — forgotten test pages and backup files are a classic misconfiguration — without probing a real target. Guessing URLs on a real site is still accessing a system in a way the owner didn't intend, even if the file was "publicly accessible" by a guessed URL. It needs written permission first.

## OSINT, with care

OSINT — open-source intelligence — is information gathered from public sources: public websites, documents, social media, code, and so on. It is a large field, and it is easy to misuse.

The safe, useful version for a learner is simple: read public documentation for a tool you use, read public guidance from OWASP, learn from public lab write-ups written by people who tested their own labs. That kind of OSINT is encouraged.

What it is not: gathering information about a real person, employee, or company to target them, collecting personal data like addresses or phone numbers, or planning social engineering. That can cause real harm, even if each piece of data was "public" on its own. Gathering, combining, and acting on personal information about a real person is not harmless just because each piece was public.

So keep your OSINT to tools, concepts, and lab systems. Don't write a report about a real person or company. Write about a lab system, a tool, or a concept.

## The thread through all of this

Recon without a question, without a scope, and without notes is noise, not recon. Recon on a real target because "it is only public information" or "I didn't change anything" is still testing, and it still needs permission. Sweeping or flooding because a tool allows it is the same mistake in lab and out of it. And sharing recon output publicly with real names, IPs, or personal data is a leak — summarise with placeholders, always.

Recon is the first step of any test, so it is where ethics starts, not where it ends. Get this part right and everything after it gets easier.

Next: `web-tools.md`, same question-first, lab-only method, applied to web testing tools for a lab website you built yourself.

---
*All examples here are for your own machine or your own private lab only. Use test data only. Never publish a real domain, real IP, real username, or real personal data in a note or public file.*
