# 03 — Networks: Network Tools, Fully Explained

## What this file is

This is Phase 2, part 2. In `network-basics.md` you learned the map: IP, ports, TCP vs UDP, DNS, HTTP. Now you learn the tools that show you that map on your own machine, in your own lab only.

If you can run each tool below, read its output line by line, and explain what it proves and what it does NOT prove, you can audit a lab network safely. If you only memorise tool names, you will not understand a real result.

No shortcuts here. Each tool gets: simple explanation, what, why, when, when NOT to use it, lab example on your own machine only, output meaning line by line, common mistakes, and a practice task.

**Safety rule for this whole file:**
All examples are on your own machine, or your own private lab network, only.
- Run `ping`, `ip`, `ss`, `dig`, `curl` against localhost (`127.0.0.1`), your own lab VM, or a lab domain you created yourself.
- Never ping-sweep, port-scan, or test networks, websites, or computers you do not own or do not have written permission to test.
- Never use these tools on public Wi-Fi, school networks, or company networks to "learn" — that is unauthorised testing, not learning.
- Use small, single checks for learning. One `ping` with a count, one `dig` query, one `curl` to a lab service — not a flood, not a sweep, not a loop.
- Use generic placeholders only in notes and screenshots: `your-user`, `<your-lab-ip>`, `<test-password>`. Never put a real username, hostname, or password in a note, screenshot, or public file.

---

## 1. The tool mindset: question first, tool second

A tool does not give an answer by itself. It gives evidence for a question.

**Practical design note:**
Design every check like this:
1. Question: What do I want to know?
2. Tool: Which tool answers that question, with the smallest check?
3. Expected output: What should I see if it works?
4. Actual output: What did I see?
5. Meaning: What does it prove, and what is still unknown?

Example questions and tools:
- "What is my own IP?" -> `ip a`
- "Is anything listening on my own machine?" -> `ss -tln`
- "Is my own machine reachable from itself?" -> `ping -c 2 127.0.0.1`
- "What does DNS say for my lab name?" -> `dig <lab-name> +short`
- "What does my lab web service return?" -> `curl -i http://127.0.0.1:<lab-port>/`

**Algorithm: CHOOSE_A_TOOL**

```
Step 1: Write the question in one sentence.
Step 2: If the question is about MY address -> use ip.
Step 3: If the question is about MY listening ports -> use ss.
Step 4: If the question is about REACHABILITY in lab -> use ping, one host only.
Step 5: If the question is about NAME -> IP -> use dig.
Step 6: If the question is about WEB RESPONSE -> use curl.
Step 7: If the question is about PACKETS on the wire in lab -> use Wireshark, lab only, with a filter.
Step 8: Run the smallest check that answers the question.
Step 9: Read the output line by line. Do not guess.
Step 10: Write down: question, command, output, meaning, next question.
```

**Common mistake:**
Running a tool because it looks powerful, without a question. That leads to scans, sweeps, and noise — and in real life, to unauthorised testing.

---

## 2. `ip` — what is my own network identity?

**What:**
`ip` shows and manages network interfaces, addresses, and routes on your own machine. For learning, you use only the read parts: `ip a`, `ip link`, `ip route`.

**Why:**
Before you can understand a network, you must know your own starting point: which interface, which IP, which gateway. Without that, every other result is confusing.

**When:**
At the start of any lab session, after connecting to a lab network, or when a lab service does not respond and you want to check your own setup first.

**When NOT to use it:**
Do not use `ip` to change settings on a machine you do not own, and do not copy an IP from a screenshot into a public note. Use placeholders in notes.

**Lab example — own machine only:**

```bash
ip a
ip link
ip route
```

**Output meaning, line by line (example, values will differ in your lab):**

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.10/24 brd 192.168.1.255 scope global eth0
```

- `2: eth0:` = interface number and name. `eth0` is usually a wired interface, `wlan0` is usually wireless. Names can differ.
- `UP, LOWER_UP` = interface is up and has a link. `DOWN` means it is off.
- `mtu 1500` = maximum packet size for this interface. You do not need to change it for learning.
- `inet 192.168.1.10/24` = your IPv4 address and subnet. `/24` means the first 24 bits are the network part. This is a private lab address in this example.
- `brd 192.168.1.255` = broadcast address for that subnet.
- `scope global` = this address is usable on that network. `scope host` for `lo` means localhost only.

For `ip route`, a line like `default via 192.168.1.1 dev eth0` means: to reach the outside, send packets to the gateway `192.168.1.1` through `eth0`.

**Loopback check:**

```bash
ip a show lo
```

You should see `127.0.0.1/8` for IPv4 and `::1/128` for IPv6 on `lo`. That is your own machine talking to itself. It is always there, even with no network cable.

**Common mistakes:**
- Confusing the interface name with the IP. `eth0` is not an IP.
- Thinking a private IP like `192.168.1.10` is your public IP. It is not. It only works inside that private network.
- Sharing a real IP screenshot publicly. In notes, write `<your-lab-ip>` instead.

**Practice task:**
Run `ip a` on your own machine only. Write down, in your own words: interface name, your private IP, and whether `lo` shows `127.0.0.1`. Do not share the real IP publicly.

---

## 3. `ss` — what is listening on my own machine?

**What:**
`ss` (socket statistics) shows sockets: listening ports, established connections, and the process listening, on your own machine.

**Why:**
This is the first audit tool. If a service is listening, it is a door. If it listens on `0.0.0.0`, it is a door open to the network. If it listens on `127.0.0.1`, it is a door open only to your own machine.

**When:**
When you want to audit your own machine, after installing a lab service, or before starting a lab to know what is already running.

**When NOT to use it:**
Do not use `ss` output to scan other machines. `ss` only shows your own machine. To check another machine you would need a scan, which you must not do without written permission.

**Lab example — own machine only:**

```bash
ss -tln
ss -tlnp
```

- `-t` = TCP only.
- `-l` = listening only.
- `-n` = numbers, not names. This avoids DNS lookups and shows ports clearly.
- `-p` = process, only if you have permission to see it in your lab.

**Output meaning, line by line (example):**

```
State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
LISTEN 0       4096    127.0.0.1:5432     0.0.0.0:*
LISTEN 0       4096    [::1]:5432         [::]:*
```

- `LISTEN` = the service is waiting for connections on that port.
- `127.0.0.1:5432` = listening on localhost only, IPv4. Safer. Only your own machine can connect.
- `[::1]:5432` = listening on localhost only, IPv6. Same idea, IPv6 loopback.
- `0.0.0.0:5432` would mean listening on all IPv4 interfaces. That is exposed to the network. Check it, understand why, and close it if it is not needed in lab.
- `5432` in this example is a database port (PostgreSQL). The port number alone does not prove which program it is — `ss -tlnp` shows the process, in your own lab.
- `0.0.0.0:*` in the peer column means any peer can try to connect, if the local side allows it. It does not mean a connection exists yet.

**Algorithm: AUDIT_MY_LISTENING_PORTS**

```
Step 1: Run ss -tln on your own machine only.
Step 2: For each LISTEN line, write down: address, port.
Step 3: If address is 127.0.0.1 or ::1 -> mark "localhost only - safer".
Step 4: If address is 0.0.0.0 or :: -> mark "exposed to network - check this".
Step 5: For each exposed port, ask: Do I need this service in lab? Who should reach it?
Step 6: If you do not need it, stop the lab service in your own lab. Do not change a system you do not own.
Step 7: Run ss -tln again. Confirm the port is gone, or now localhost only.
Step 8: Write a short note: port, address, verdict, action. Use placeholders, no real hostname.
```

**Common mistakes:**
- Thinking `LISTEN` means "hacked" or "attacked". It only means a service is waiting.
- Thinking a closed port and a filtered port are the same. Closed = no service, connection refused. Filtered = firewall drops it, no reply. `ss` does not show remote ports at all.
- Sharing full `ss` output publicly with real IPs and hostnames. Summarise with placeholders instead.

**Practice task:**
Run `ss -tln` on your own machine only. Count how many ports are localhost only, and how many are `0.0.0.0`. For each exposed one, write one sentence: what lab service is it, and do you need it?

---

## 4. `ping` — is my lab host reachable, one host at a time?

**What:**
`ping` sends small ICMP echo requests to one host and measures replies. It tests reachability and round-trip time, nothing more.

**Why:**
It is the simplest reachability test. If `ping` to localhost works but `ping` to a lab VM fails, the problem is not your own machine — it is the path, the VM, or the firewall in lab.

**When:**
One host, one small count, in your own lab. Example: `ping -c 2 127.0.0.1` to test your own stack, or `ping -c 2 <your-lab-vm-ip>` to test one lab VM you own.

**When NOT to use it:**
Never ping-sweep a range (`ping` in a loop over many IPs), never flood, never ping a public site repeatedly to "test speed". That is noise, and on other networks it is unauthorised probing.

**Lab example — own machine only:**

```bash
ping -c 2 127.0.0.1
```

**Output meaning, line by line (example):**

```
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.045 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.038 ms

--- 127.0.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
```

- `PING 127.0.0.1` = target. Localhost should always reply.
- `64 bytes from 127.0.0.1: icmp_seq=1` = one reply received, sequence 1.
- `ttl=64` = time to live left. It decreases by 1 per router hop. A high ttl on localhost is normal.
- `time=0.045 ms` = round-trip time. Localhost is very fast. A lab VM on the same network is usually under a few milliseconds.
- `2 packets transmitted, 2 received, 0% packet loss` = all replies came back. That proves reachability for ICMP, nothing else.
- `100% packet loss` does NOT prove a host is down. Many firewalls block ICMP. It proves only that ICMP replies did not come back.

**What ping does NOT prove:**
- It does not prove a web service is running. A host can reply to `ping` and have no web port open.
- It does not prove a port is open. Use `ss` on your own machine, or a lab service check with permission, for ports.
- It does not prove a host is safe. Reachability is not trust.

**Common mistakes:**
- Using `ping` without `-c` and letting it run forever. Always use `-c 2` or `-c 4` for learning.
- Ping-sweeping a subnet to "find hosts". Do not do this outside your own private lab, and even in lab, prefer checking your lab inventory list instead of sweeping.
- Assuming no ping reply means no host. Firewalls often drop ICMP by design.

**Practice task:**
Run `ping -c 2 127.0.0.1` on your own machine only. Write down: packets transmitted, received, packet loss, and one sentence about what it proves.

---

## 5. `dig` and `nslookup` — what does DNS say, for one name at a time?

**What:**
`dig` asks DNS for records for one name. `nslookup` does a similar job, with simpler output, and is available on many systems.

**Why:**
DNS is the phonebook. When a web journey fails, you need to know: did DNS give an IP, or did DNS fail first? `dig` separates a DNS problem from a web problem.

**When:**
One lab name at a time, with a small query. Example: `dig <your-lab-name> +short`, or `dig example.com +short` for a public learning example only, not as a test target.

**When NOT to use it:**
Do not run DNS queries in a loop, do not enumerate many subdomains, do not query for private internal names you do not own. One learning query is enough.

**Lab example — one query only:**

```bash
dig example.com +short
dig example.com A
nslookup example.com
```

**Output meaning, line by line (example, values change over time):**

```
93.184.216.34
```

With `+short`, `dig` prints only the answer: the IPv4 address for `example.com` in this example. Without `+short`, you get sections:

- `QUESTION SECTION` = what you asked: name, type (A, AAAA, MX, etc.).
- `ANSWER SECTION` = what DNS returned: name, TTL, type, value.
- `AUTHORITY SECTION` = which nameserver is authoritative for that zone.
- `ADDITIONAL SECTION` = extra records, like the nameserver's own IP.
- `Query time` = how long DNS took. A first query is slower, a cached query is faster.
- `SERVER` = which DNS server answered you. In lab, this is often your router or lab DNS.
- `status: NOERROR` = the query succeeded. `NXDOMAIN` means the name does not exist. `SERVFAIL` means the server failed to answer.

**DNS record types, simply:**
- `A` = IPv4 address.
- `AAAA` = IPv6 address.
- `CNAME` = alias, one name points to another name.
- `MX` = mail server for a domain.
- `TXT` = text, often used for verification.
- `NS` = nameserver for a domain.

**Common mistakes:**
- Thinking `dig` proves a website is up. It only proves DNS gave an answer. The web service can still be down.
- Thinking a cached answer is always fresh. TTL tells you how long it can be cached. After TTL, a new query is needed.
- Running many `dig` queries to "find hidden subdomains". That is enumeration, not a single learning check. Do not do it without written permission.

**Practice task:**
Run one `dig example.com +short` query. Write down the answer and the record type. Then run `dig example.com A` without `+short` and find the `ANSWER SECTION`. What is the TTL?

---

## 6. `traceroute` / `tracert` — what path does a packet take, in lab?

**What:**
`traceroute` (Linux) / `tracert` (Windows) shows the routers a packet passes through to reach one destination, by using TTL values. Each hop replies when TTL expires.

**Why:**
It explains "where does it stop?" If a lab connection fails after hop 2, the problem is near hop 2, not at the destination.

**When:**
One lab destination at a time, for learning the path concept. In many labs, `traceroute` to localhost is not useful — the path is just your own machine. Use it to understand the concept, not to trace public sites repeatedly.

**When NOT to use it:**
Do not trace many destinations, do not trace a target you do not own, and do not use it to map a real network. That is reconnaissance, not a learning check.

**Concept example, not a command to run on a real target:**

```
Hop 1: your lab gateway
Hop 2: lab router
Hop 3: lab VM
```

Each hop adds time. A `* * *` line means that hop did not reply to that probe — often because it blocks ICMP, not because the path is broken. The next hop can still reply.

**Common mistakes:**
- Thinking `* * *` means the network is down. It often means a router hides itself.
- Thinking traceroute shows the exact physical path. It shows logical hops, which can change between runs.
- Using traceroute as a scanning tool. It is a path tool, one destination at a time.

**Practice task:**
Draw a 3-hop lab path on paper: your machine -> lab gateway -> lab VM. Label each hop with its role. Do not run traceroute against a real site for this task.

---

## 7. `curl` — what does a lab web service actually return?

**What:**
`curl` makes an HTTP request and shows the response: headers, status code, and body. It is a client, like a browser without the visual page.

**Why:**
A browser hides details. `curl -i` shows them: status code, server header, content type, and the actual body. That is how you learn HTTP, and how you test a lab web service you built yourself.

**When:**
Against your own lab web service only, or a lab service you have written permission to test. Example: a simple lab page running on `127.0.0.1` on a lab port you chose.

**When NOT to use it:**
Do not use `curl` to test real websites for vulnerabilities, do not send crafted requests to real sites, do not download files from untrusted sites in lab. A single `curl` to a public example for learning HTTP headers is different from testing a real target — keep learning checks minimal, and lab-first.

**Lab example — own lab service only:**

```bash
curl -i http://127.0.0.1:8000/
```

Replace `8000` with the port of your own lab service, if you have one running. If you have no lab service, do not invent one — first learn HTTP from `network-basics.md`, then build a simple lab page later in the web phase.

**Output meaning, line by line (example):**

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 123

<html>...</html>
```

- `HTTP/1.1 200 OK` = protocol, status code 200, reason OK. 200 means success.
- `Content-Type: text/html` = the body is HTML. Other types: `application/json`, `text/plain`, etc.
- `Content-Length: 123` = body size in bytes.
- Blank line = end of headers, start of body.
- Body = the actual page content, HTML or JSON or text.

Other status codes you will meet in lab:
- `301 / 302` = redirect, go to another URL.
- `404` = not found, that path does not exist on that service.
- `403` = forbidden, you are not allowed to see it.
- `500` = server error, the service failed internally.

**Common mistakes:**
- Thinking `curl` returning a page means the service is secure. It only means it responded.
- Sending passwords, tokens, or real data with `curl` in a public note or screenshot. Never do that. Use `<test-password>` placeholders, and test data only.
- Using `curl` to download and run a script from the internet without reading it. Never run a downloaded script blindly.

**Practice task:**
If you have a lab web service on localhost, run `curl -i` against it once. Write down the status code and content type. If you do not have one yet, write down what 200, 404, and 500 mean, in your own words.

---

## 8. `arp` — how does a lab machine find a neighbour's MAC?

**What:**
`arp` shows the ARP table: which IP addresses on your local network map to which MAC addresses, on your own machine.

**Why:**
This is the link between Layer 3 (IP) and Layer 2 (MAC) from `network-basics.md`. When your machine wants to send a packet to a neighbour on the same subnet, it needs that neighbour's MAC. ARP is how it asks.

**When:**
To understand a lab network, after you have already used `ip` and `ping` in lab. Read-only: `arp -a` or `ip neigh`.

**When NOT to use it:**
Do not use ARP to spoof, poison, or redirect traffic. ARP spoofing is an attack, not a learning check. Do not run ARP tools against a network you do not own.

**Lab example — own machine only:**

```bash
ip neigh
arp -a
```

**Output meaning (example, values differ in lab):**

```
192.168.1.1 dev eth0 lladdr aa:bb:cc:dd:ee:ff REACHABLE
```

- `192.168.1.1` = neighbour IP, often the gateway in lab.
- `lladdr aa:bb:cc:dd:ee:ff` = neighbour MAC address, in this example a placeholder format. Real MACs are unique to a device.
- `REACHABLE` = this entry is fresh and was recently confirmed. `STALE` means it is old and will be re-checked on next use. `FAILED` means it could not be reached.

**Common mistake:**
Thinking ARP works across the internet. It does not. ARP only works on the same local subnet. Across routers, the gateway's MAC is used instead.

---

## 9. Wireshark — what do packets look like, in lab only?

**What:**
Wireshark is a packet analyser. It captures packets on an interface in your own lab and shows them decoded, layer by layer: Ethernet, IP, TCP/UDP, HTTP, DNS, and more.

**Why:**
Tools like `ping`, `dig`, and `curl` give you summaries. Wireshark shows you the actual packets behind those summaries. Seeing a DNS query packet, a TCP handshake, and an HTTP request packet, once, in lab, makes the whole network map real.

**When:**
In your own private lab only, capturing traffic from your own machines, with a filter to keep it small. Example filter: `dns` to see only DNS, `http` to see only HTTP, `ip.addr == <your-lab-ip>` to see only your lab host.

**When NOT to use it:**
Never capture traffic on a shared, school, company, or public network. Never capture other people's traffic. Never capture passwords, tokens, or private data, even in lab — use test data only, and stop the capture as soon as you have the learning example.

**Lab concept — no real capture target here:**
1. Start a lab service on your own machine, with test data only.
2. Start Wireshark, select the lab interface, apply a filter like `dns` or `tcp.port == <your-lab-port>`.
3. Run one small lab check: one `dig`, one `curl` to localhost lab service.
4. Stop the capture immediately.
5. Click one DNS packet. Read: source IP, destination IP, query name, query type.
6. Click one TCP packet. Read: source port, destination port, flags (SYN, ACK).
7. Click one HTTP packet. Read: method, path, status code.
8. Close Wireshark. Do not save a capture with real data. If you save for notes, use a lab-only capture with test data, and do not publish it.

**What Wireshark does NOT do:**
- It does not hack a network. It only shows packets you are allowed to capture in lab.
- It does not decrypt HTTPS by magic. HTTPS bodies are encrypted. You will see TLS handshake packets, not the plaintext body, unless you set up a lab with your own keys — that is an advanced lab, not a first check.
- It does not prove a network is secure. It proves what packets looked like in that capture.

**Common mistakes:**
- Capturing without a filter, on a busy network, and drowning in packets. Always filter.
- Capturing other people's traffic. That is a privacy violation, not learning.
- Saving and sharing a capture publicly. Do not publish packet captures. Summarise the learning in words, with placeholders.

**Practice task:**
In your own lab only, with a filter, capture one DNS query you make yourself. Write down, in words: query name, query type, answer IP (or placeholder if you use a lab name). Do not publish the capture file.

---

## 10. Putting it together: the full network audit algorithm

Use this algorithm once, slowly, on your own machine only, after you have practised each tool separately.

**Algorithm: NETWORK_AUDIT_MY_MACHINE**

```
Step 1: Question: What is my own network setup today?
Step 2: Run ip a. Write: interface, private IP (as <your-lab-ip> in public notes), gateway from ip route.
Step 3: Run ss -tln. Write: each listening port, localhost only or exposed.
Step 4: For each exposed port, ask: Do I need this lab service? If not, stop it in lab.
Step 5: Run ping -c 2 127.0.0.1. Confirm: 0% packet loss for localhost.
Step 6: Run dig <your-lab-name> +short, one query only, if you have a lab name. If not, run one dig example.com +short as a learning example.
Step 7: If you have a lab web service, run curl -i against localhost lab service once. Write: status code, content type.
Step 8: Run ip neigh. Write: gateway IP and its state, in lab terms.
Step 9: Optional, lab only: Wireshark with a filter, one small capture, then stop. Write what you saw, in words.
Step 10: Review your notes. Ask: What is still unknown? What is the next question?
Step 11: Clean up: stop lab services you do not need, close Wireshark, do not publish real IPs, hostnames, or passwords.
```

**Design note:**
A good audit is boring, in a good way: small checks, clear notes, no surprises. A bad audit is a flood of scans with no question, no notes, and no understanding. Red-team work, later, is built on this boring, careful audit skill — but always with written permission, in a lab or authorised scope, never on other people's networks.

---

## 11. Common mistakes across all network tools

- Scanning a network you do not own, because a tool makes it easy. Ease is not permission.
- Running a tool in a loop, flood, or sweep, because you want "more data". More data without a question is noise.
- Trusting one tool alone. `ping` success does not prove a port is open. `dig` success does not prove a website is up. Cross-check with a second tool.
- Copying real output into a public note, screenshot, or repo. Use placeholders: `<your-lab-ip>`, `your-user`, `<test-password>`, `<lab-port>`.
- Changing a setting on a machine you do not own, because a tool allowed it. Do not change, only read, in learning.
- Forgetting to stop a lab service after practice. A lab service left listening on `0.0.0.0` is an open door you forgot about.
- Thinking a tool result is a verdict on a whole network. It is evidence for one question, at one time, in one lab.

---

## 12. Practice tasks — lab only, one tool at a time

Do these in order, on your own machine only. Write the answer in your own words, not by copying output publicly.

1. `ip a`: What is your interface name? What is your private IP range? Is `lo` present with `127.0.0.1`?
2. `ss -tln`: How many ports are localhost only? How many are exposed? For each exposed port, what lab service is it?
3. `ping -c 2 127.0.0.1`: What is the packet loss? What does it prove?
4. `dig example.com +short`: What IP did you get? What record type is it?
5. `curl -i` to your own lab service, if you have one: What status code and content type did you get? If you do not have one yet, explain 200, 404, and 500.
6. `ip neigh`: What is your gateway's state in lab terms?
7. Wireshark, lab only, with a filter: What did one DNS packet show? Write it in words, no capture file published.
8. Full audit: Run the NETWORK_AUDIT_MY_MACHINE algorithm once, slowly, and write a 5-line summary with placeholders only.

---

## 13. Checklist — you are ready to move on when you can do all of these

- [ ] I can explain, in simple words, what `ip`, `ss`, `ping`, `dig`, `curl`, and `arp` each do, and what question each answers.
- [ ] I can run `ip a` and `ss -tln` on my own machine and read the output line by line.
- [ ] I can tell, from `ss` output, whether a port is localhost only or exposed to the network, and why that matters.
- [ ] I can run `ping -c 2` on localhost and explain packet loss correctly, without over-claiming.
- [ ] I can run one `dig` query and find the ANSWER SECTION, record type, and TTL.
- [ ] I can run `curl -i` on a lab service and explain the status code and content type.
- [ ] I can explain, simply, what Wireshark shows, and why it must only be used in my own lab with a filter and test data.
- [ ] I can run the full NETWORK_AUDIT_MY_MACHINE algorithm once, slowly, with clear notes using placeholders only.
- [ ] I have not scanned, swept, or tested any network, website, or computer I do not own or do not have written permission to test.
- [ ] My notes contain no real username, hostname, password, or real IP — only generic placeholders.

**Next file:**
After this, move to Phase 3: Web basics and OWASP, simply explained, lab only. You will use `curl` and DNS knowledge there, but only against your own lab web service.

---
*All examples in this file are for your own machine or your own private lab only. Use test data only. Never publish a real username, hostname, password, or real IP in a note or public file.*
