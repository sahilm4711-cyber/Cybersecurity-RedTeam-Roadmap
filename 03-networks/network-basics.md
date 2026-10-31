# 03 — Networks: Network Basics, Fully Explained

## What this file is

This is Phase 2, part 1. You already know computers and Linux. Now you learn how computers talk to each other.

If you can explain this file in simple English, draw a lab network, and check your own machine's network safely, you have the map for everything after it: web, tools, AD, and red-team lab work. Without this map, tools are just noise.

No shortcuts here. Each concept gets: simple explanation, practical design notes, algorithm, lab example on your own machine only, and common mistakes.

**Safety rule for this whole file:**
All network checks are on your own machine, or your own private lab network, only.
- Check your own IPs, your own listening ports, your own lab VMs.
- Never scan, ping-sweep, or test networks, websites, or computers you do not own or do not have written permission to test.
- Never use network tools on public Wi-Fi, school networks, or company networks to "learn" — that is unauthorised testing, not learning.

---

## 1. A network, in one picture

Think of a network as a postal system for computers:

- **Computer = a house.**
  Each house has an address, so post can reach it.

- **IP address = house address.**
  Example: `192.168.1.10` is a private house address in a home/lab network. `127.0.0.1` is a special address meaning "this house itself, this computer only".

- **Port = a door in the house, for a specific service.**
  Example: Port 80 = web door, 443 = secure web door, 22 = SSH door, 5432 = PostgreSQL database door (in lab terms).
  A house can have many doors, some open (service listening), some closed (no service).

- **Router = post office / main gate.**
  It connects your home/lab network to other networks, and decides where post goes next.

- **Switch = internal mail sorter in a building.**
  It connects computers inside one local network, and delivers post to the right house inside that network, without sending it outside unnecessarily.

- **DNS = phone book.**
  You type a name like `example.com`, DNS looks up the IP address for that name, so your computer knows which house to send post to.

- **Packet = one envelope in the post.**
  Data is broken into small packets, sent one by one, and put back together at the destination. If one envelope is lost, TCP will resend it (see Section 4).

**Practical design note:**
Every network security question starts with this picture: Which houses (computers) exist in this lab? Which doors (ports) are open, and why? Who is allowed to come through each door? Which doors should be closed, or localhost-only? If you cannot draw the picture for a lab network, you cannot test it safely or explain it clearly.

---

## 2. IP addresses — fully, simply

### 2.1 What is an IP address?

An IP address is a number that identifies a computer on a network, so other computers can send data to it.

IPv4 example, the common form you will see first:
```
192.168.1.10
```
- Four numbers, separated by dots, each 0 to 255.
- First numbers often identify the network, last numbers identify the computer inside that network — exact split depends on subnet mask (see Section 2.4, simplified).

IPv6 example, the newer, longer form:
```
::1
```
- `::1` is IPv6 localhost, same idea as `127.0.0.1` in IPv4: this computer only.
- You will see `[::1]:5432` in `ss` output for localhost services in IPv6 form. Same meaning as `127.0.0.1:5432`: localhost only, safer.

### 2.2 Private vs public, simply

- **Private IP:** Used inside home/lab/office networks only. Not directly reachable from the whole internet (router protects it, by design).
  Common private ranges you will see in labs:
  - `10.0.0.0` to `10.255.255.255` — large private networks, labs, companies.
  - `172.16.0.0` to `172.31.255.255` — medium private networks.
  - `192.168.0.0` to `192.168.255.255` — home networks, small labs. Example: `192.168.1.10` is very common at home.
- **Public IP:** Used on the whole internet, assigned by your internet provider. Your router usually has a public IP on the outside, and private IPs on the inside for your computers. That separation is a security design: inside computers are not directly exposed, unless a door (port) is forwarded on purpose — and forwarding must be a deliberate, understood decision, never an accident in lab.
- **Localhost:** `127.0.0.1` (IPv4) and `::1` (IPv6) always mean "this computer itself". No network cable, no Wi-Fi needed. A service listening only on localhost can only be reached from that same computer. That is why your PostgreSQL example on `127.0.0.1:5432` was safer: only your own machine could connect to it in lab terms.

### 2.3 How to check your own IP, lab only

```bash
ip a
hostname -I
```

What these mean:
- `ip a` = show all network interfaces and their IPs on your own machine. Interface names like `lo` (loopback, localhost), `eth0` or `wlan0` (wired/Wi-Fi, names vary), are normal.
- `hostname -I` = show your own machine's IPs, short form, one line.
- Look for: `lo` with `127.0.0.1`, and your main interface with a private IP like `192.168.x.x` in a home/lab network. That private IP is your lab address, not a public secret to share carelessly, but also not a password — do not post your public IP or lab details publicly without thinking, as a habit.

**Lab task — own machine only:**
1. Run `ip a`, note your interfaces and IPs.
2. Draw: Your computer -> Router -> Internet (for home) OR Your Kali VM -> Private Lab Network -> Lab Server VM (for lab). Label IPs you found (private/lab only, do not share public IP in notes you publish).
3. Write in notes: Which IP is localhost? Which is private? Which interface is which?

### 2.4 Subnet mask and CIDR, simply (not deep maths yet)

You will see notations like `192.168.1.10/24`. The `/24` is CIDR notation, short for subnet mask.

Simple idea, without deep maths:
- `/24` means: first 24 bits identify the network, remaining bits identify computers inside that network.
- In a `/24` home/lab network, computers usually share first three numbers, like `192.168.1.x`, where `x` changes per computer: `.1` might be router, `.10` your Kali, `.20` your lab server, etc.
- Smaller number after `/` = larger network, more computers possible. Larger number = smaller network, fewer computers.
- You do not need to calculate subnets by hand perfectly as a beginner. You need to understand: CIDR tells you which computers are "in the same local network" vs "in a different network, via router". That distinction matters for lab design: local network traffic stays local, other network traffic goes via router.

**Practical design note:**
Lab networks should be small and private by design: a `/24` private lab network, with only your lab VMs inside it, is a good, simple design for learning. Do not put lab vulnerable VMs on a large shared network, or on a network with real computers you do not own.

---

## 3. Ports — doors, services, and listening, fully

### 3.1 What is a port?

A port is a number, 0 to 65535, that identifies a specific service on a computer.

Think: IP address gets you to the right house. Port gets you to the right door/service inside that house.

- **Well-known ports (0-1023):** Reserved for common services, need higher privilege to listen on in traditional design.
  - 21 = FTP (old file transfer, insecure by design in plain form)
  - 22 = SSH (secure remote shell)
  - 23 = Telnet (old, insecure, do not use for real work)
  - 25 = SMTP (email sending)
  - 53 = DNS
  - 80 = HTTP (web, not encrypted)
  - 110 = POP3 (email receiving, old)
  - 143 = IMAP (email receiving)
  - 443 = HTTPS (web, encrypted)
  - 3306 = MySQL database
  - 5432 = PostgreSQL database (your lab example)
- **Registered ports (1024-49151):** For other services, registered but not as universal as well-known.
- **Dynamic / private ports (49152-65535):** For temporary connections, like when your browser connects out, it uses a temporary high port as its return door.

You do not need to memorise all ports. Memorise the common ones above, and learn to look up others in documentation when you meet them in lab output. That is professional habit: common in memory, rest in docs.

### 3.2 Listening, open, closed, filtered — simple meanings

When a tool checks a port in a lab, it can report different states. Understand these words precisely, because beginners confuse them:

- **Listening (from `ss` on own machine):** A service on your own machine is waiting on that port, ready to accept connections. Example: `ss -tln` shows `127.0.0.1:5432` LISTEN = PostgreSQL service is listening on localhost port 5432.
- **Open (from a lab scan of a lab target you own):** A service answered on that port. Open means service present, not automatically vulnerable. Next questions: what service, what version, should it be open, to whom?
- **Closed:** No service answered on that port, but the computer responded "no service here". Closed is different from filtered: closed tells you computer is reachable, but that door is shut.
- **Filtered:** No clear answer, likely a firewall blocked the check, or dropped it silently. Filtered does not prove open or closed — it proves you cannot tell from outside, which is often a firewall design working as intended.

**Practical design note — the audit question for every listening port:**
For each listening port on your own machine in lab, ask:
1. What service is this? (name, version if known in lab)
2. Should this service be running at all in this lab?
3. Should it listen on localhost only, or on all networks? Why?
4. Is it patched/updated in lab terms?
5. If it should not be listening, or should be localhost-only but is exposed, that is a lab finding, with a fix idea: stop service, or bind to localhost, or firewall it, in lab design.

That five-question check is more valuable than just listing ports. It is the difference between a tool output and a professional audit.

### 3.3 TCP vs UDP — reliable vs fast, simply

- **TCP (Transmission Control Protocol):**
  - Reliable, ordered, checked delivery. Like a phone call: you connect first, you talk, you confirm you heard, if a word is lost, it is resent.
  - Used for: web (HTTP/HTTPS), email, SSH, databases — where every piece of data must arrive correctly, in order.
  - How it works, simply: Three-way handshake to connect (SYN -> SYN-ACK -> ACK), then data, then close. You do not need to memorise handshake packets perfectly yet, just understand: TCP connects first, then talks, then confirms.
- **UDP (User Datagram Protocol):**
  - Fast, unchecked, no connection first. Like a postcard: you send it, you hope it arrives, you do not confirm each postcard.
  - Used for: DNS queries, video streaming, online games, VoIP — where speed matters more than perfect delivery of every single piece, or where the application itself handles retries if needed.
  - Security note: Because UDP has no connection handshake, UDP services can be abused for amplification in real attacks — that is why understanding UDP matters for defence, but you will not practise UDP attacks. You will practise understanding UDP services in lab, and checking if they should be exposed.

**Practical design note:**
When you see `ss -tln` (TCP listening) vs `ss -uln` (UDP listening, in lab if you try it), remember: TCP listening = service waiting for a connection. UDP listening = service waiting for datagrams, no connection step. Both need the same audit questions: what service, should it run, localhost-only or exposed?

---

## 4. DNS, DHCP, and how a name becomes a connection — step by step

### 4.1 DNS — phone book for the internet, simply

DNS (Domain Name System) translates names to IPs, and IPs to names sometimes.

Example journey, in lab terms:
1. You type `example.com` in browser (or a lab name in lab).
2. Your computer asks a DNS server: "What IP is example.com?"
3. DNS server answers, for example: "93.184.216.34" (example IP, not to memorise).
4. Your computer then connects to that IP on port 80 or 443 for web.

**Types of DNS records you will meet first (simple meanings):**
- **A record:** Name -> IPv4 address. Example: `example.com` -> `93.184.216.34`.
- **AAAA record:** Name -> IPv6 address.
- **CNAME record:** Name -> another name (alias). Example: `www.example.com` -> `example.com`.
- **MX record:** Name -> mail server for that domain.
- **TXT record:** Name -> text data, often used for verification, like proving you own a domain in lab platforms.

You do not need to memorise all DNS record types yet. Learn A, AAAA, CNAME first, then MX/TXT when you meet them in lab tools output.

**Practical design note — DNS security in simple terms:**
DNS was designed for convenience, not security originally. That is why DNS spoofing/poisoning (tricking a computer into accepting a wrong IP for a name) is a known attack concept in labs, and why DNS over HTTPS (DoH) and DNSSEC (signed DNS answers) exist as defences. In lab, learn normal DNS first, then learn why signed/encrypted DNS helps, in concept, not by attacking real DNS servers.

### 4.2 DHCP — automatic address assignment, simply

DHCP (Dynamic Host Configuration Protocol) automatically gives computers on a network an IP address, subnet mask, gateway (router) address, and DNS server address, when they join the network.

Simple idea: Instead of manually typing an IP for every computer in a lab, DHCP server hands out addresses automatically, from a pool, for a limited time (lease).

Lab example: When your Kali VM starts on a home/lab network, it usually gets a private IP automatically via DHCP from the router. That is why `ip a` shows a private IP without you typing it.

Security note in lab terms: A rogue DHCP server (a fake one on a network you do not own) could hand out wrong gateway/DNS and redirect traffic — that is a known attack concept, and why you should only join networks you trust, and only run DHCP servers in your own private lab, never on a shared network without permission.

### 4.3 Full journey — what happens when you open a website in lab, step by step

This is the most important algorithm in this file. Memorise the journey, not just the words.

```
ALGORITHM: OPEN_A_WEBSITE_IN_LAB

INPUT: A name, for example https://example.com (or a lab site name in your lab)
OUTPUT: A page shown in browser

1. DNS LOOKUP
   - Browser asks DNS: "What IP is example.com?"
   - DNS answers with an IP, for example 93.184.216.34.
   - If DNS fails, browser shows "cannot find site" — DNS problem, not web server problem. Learn to tell the difference.

2. TCP CONNECTION
   - Browser connects to that IP on port 443 for HTTPS (or 80 for HTTP).
   - TCP handshake happens: connect, confirm, ready.
   - If connection fails, browser shows "cannot connect" — server down, port closed/filtered, or network problem. Different error, different cause.

3. TLS HANDSHAKE (for HTTPS only)
   - Browser and server agree on encryption, server shows its certificate.
   - Browser checks certificate: is it for this name? Is it trusted? Is it expired?
   - If certificate check fails, browser warns you. Do not ignore certificate warnings on real sites, and do not practise bypassing them on real sites. In lab, learn what a valid vs invalid certificate looks like on a lab site you own.

4. HTTP REQUEST
   - Browser sends a request: method (GET for a page), path (/), headers (browser type, cookies, etc.).
   - Example request idea: "GET / HTTP/1.1, Host: example.com, User-Agent: ..."

5. SERVER PROCESSING
   - Server receives request, checks what page/data is asked for, may talk to a database, may check session/cookie if you are logged in (lab only).
   - Server decides: OK (200), Not Found (404), Forbidden (403), Server Error (500), etc.

6. HTTP RESPONSE
   - Server sends response: status code, headers, body (HTML page, image, data).
   - Browser receives body, renders page: HTML structure, CSS style, JavaScript behaviour (if any).

7. REPEAT FOR EACH RESOURCE
   - A modern page loads many resources: images, CSS, JS files, each as a separate request, following steps 2-6 again (often reusing connection).
   - That is why browser developer tools in lab show many requests for one page, not just one.

END ALGORITHM
```

**Practical design note:**
When a website does not load in lab, use this journey to debug in order, not randomly:
- DNS problem? (can you resolve name with `dig` in lab?)
- Connection problem? (can you reach IP/port in lab?)
- TLS problem? (certificate warning?)
- HTTP problem? (status code 404/403/500?)
- Content problem? (page loads but looks broken?)
Debugging in journey order saves hours, and it is exactly how professionals troubleshoot networks and web apps.

---

## 5. Models — OSI and TCP/IP, simplified (not memorised in full yet)

Beginners are often told to memorise OSI 7 layers perfectly on day one. That is a shortcut to forgetting. Learn the idea first, names second, details later when you need them for Wireshark or advanced networking.

### 5.1 TCP/IP model — 4 layers, practical for red teamers

From top (user) to bottom (wire), simplified:

1. **Application Layer:** What the user/program wants. Examples: HTTP (web), DNS (names), SSH (remote shell), SMTP (email).
   - Question: What service is being used?
2. **Transport Layer:** How data is delivered reliably or fast. TCP vs UDP (Section 3.3).
   - Question: TCP or UDP? Which port?
3. **Internet Layer:** How data gets from one network to another. IP addresses, routing.
   - Question: Which IP? Which network? Via which router?
4. **Link Layer:** How data moves on one local network link. Ethernet, Wi-Fi, MAC addresses (see below).
   - Question: Which local device, by MAC address, on this local link?

### 5.2 OSI model — 7 layers, for reference, not for memorising today

OSI layers, top to bottom, for when you meet them in documentation or Wireshark:
7. Application, 6. Presentation, 5. Session, 4. Transport, 3. Network, 2. Data Link, 1. Physical.

Simple memory trick many students use: "All People Seem To Need Data Processing" (top to bottom, first letters A-P-S-T-N-D-P). Use it if it helps, but do not spend a week memorising OSI before you understand TCP/IP journey above. TCP/IP model is what the real internet actually uses, and it is enough for Phase 2.

### 5.3 MAC address — hardware address, simply

- MAC (Media Access Control) address is a hardware address burned into a network card, like a serial number for that card.
- Example form: `aa:bb:cc:dd:ee:ff` (six pairs of hex digits, example only, not a real device).
- MAC is used on a local network link to deliver data to the right physical device, after IP has routed data to the right network.
- ARP (Address Resolution Protocol) is how a computer asks on a local network: "Who has IP 192.168.1.10? Tell me your MAC, so I can send you data locally." That is a local question, not an internet question.
- Security note in lab terms: MAC addresses can be changed (spoofed) in software on your own lab machine, which is why MAC alone is not a strong security identity. Do not rely on MAC filtering alone as a defence in real design, and do not practise MAC spoofing on networks you do not own.

---

## 6. Firewall, VPN, and NAT — security designs, simply

### 6.1 Firewall — a gate with rules

A firewall is a filter that decides which network traffic is allowed in, allowed out, or blocked, based on rules.

Simple firewall rules, in lab terms:
- Allow web out (port 443 out) for browsing.
- Block incoming connections to all ports, except those deliberately allowed for a lab service.
- Allow localhost traffic always (your own computer talking to itself should not be blocked by its own firewall, by design).

**Practical design note:**
Default-deny is a good firewall design: block everything by default, then allow only what is needed, one rule at a time, with a note why each rule exists. Default-allow (allow everything, block only known bad) is weaker, because new problems are allowed until someone blocks them. When you audit a lab firewall later, ask: "Is this default-deny? Is each allow rule documented and needed?"

### 6.2 NAT — sharing one public IP, simply

NAT (Network Address Translation) lets many private computers inside a network share one public IP on the outside.

Simple idea: Inside computers have private IPs (192.168.x.x). Router has one public IP outside. When an inside computer sends data out, router changes source IP to its public IP, remembers which inside computer asked, and when reply comes back, router changes destination back to that inside computer's private IP.

Why it matters for security design: NAT hides inside private IPs from the outside by default, but it is not a firewall by itself. A service forwarded through NAT (port forwarding) becomes reachable from outside on purpose — that forwarding must be deliberate, documented, and checked, never accidental in lab.

### 6.3 VPN — private tunnel over public network, simply

VPN (Virtual Private Network) creates an encrypted tunnel between your computer and another network, over the public internet, so traffic inside the tunnel is protected from eavesdropping on the way.

Simple idea: Normal internet traffic is like postcards anyone on the way could read. VPN traffic is like a sealed envelope inside a tunnel — outsiders see a tunnel exists, but not the letters inside (if VPN is designed correctly).

Why red teamers learn VPN concept:
- Companies use VPN for remote workers to reach office network safely.
- Lab platforms sometimes use VPN to connect your lab VM to their lab network safely.
- VPN is not magic: if VPN itself has a weak design, or if a user connects to a malicious VPN, security can fail. Learn VPN as a tool with a design, not as automatic safety.

**Practical design note for labs:**
Never connect a lab vulnerable VM directly to the internet without a firewall/NAT design, and never use a VPN you do not trust for lab work with sensitive notes. Lab design = private lab network, NAT/firewall by default, VPN only when a trusted lab platform requires it, and only for that lab's network.

---

## 7. Algorithm — How to understand any new network (lab only)

Use this algorithm for every new lab network you meet. It is the network version of the Understand-A-System algorithm from computer basics.

```
ALGORITHM: UNDERSTAND_A_NETWORK (lab only)

INPUT: A lab network you own, for example your Kali VM + one lab server VM on a private network
OUTPUT: A network diagram + notes you can explain

1. IDENTIFY YOUR OWN MACHINE
   - Run: ip a, hostname -I (own machine only)
   - Note: your private IP, your interfaces, your localhost IP.

2. DRAW THE MAP
   - Draw boxes: Your Kali VM, Router/Gateway, Lab Server VM, Internet (if any, for home context only).
   - Draw lines: which box connects to which, wired or Wi-Fi, private lab network or home network?
   - Label each box with its private/lab IP, and each line with what traffic flows there.

3. CHECK LISTENING SERVICES (own machine only)
   - Run: ss -tln (own machine only)
   - For each listening port, note: port, service guess, localhost-only or exposed, should it be running?

4. CHECK NAME RESOLUTION (lab only)
   - Run: dig for a lab name or a public learning name, in lab context, as guided by network-tools file.
   - Note: what IP did DNS return? Did it match expectation? If not, why?

5. TRACE THE JOURNEY
   - Pick one simple journey: "My Kali asks for a lab web page on lab server."
   - Walk through Section 4.3 journey: DNS -> TCP -> HTTP request -> response -> render.
   - Note where each step happens in your diagram: which box does DNS, which box is server, which port is used?

6. CHECK THE GATE
   - Ask firewall/NAT design questions for this lab:
     - What is allowed in? What is allowed out? What is blocked by default?
     - Is any lab service forwarded to outside? Should it be? If yes, why, and is it documented?
   - If you cannot answer, that is a gap to learn, not a failure — note it as a question for next study.

7. EXPLAIN IT
   - Explain the network in 3 minutes, in simple English, without notes:
     "This lab network has ____ computers. My Kali IP is ____. Lab server IP is ____. Services listening on my machine are ____, localhost-only or exposed because ____. A web request in this lab goes ____ -> ____ -> ____."
   - If you cannot explain a part, that part goes back to Step 1.

END ALGORITHM
```

---

## 8. Lab tasks — do these on your own machine / private lab only

**Task 1 — Your own network:**
```bash
ip a
hostname -I
ss -tln
```
Write in notes: interfaces, private IP, localhost IP, listening ports with localhost-only vs exposed note for each.

**Task 2 — Draw your lab network:**
Draw on paper or in notes: Kali VM, router/gateway, lab server (if you have one), internet (if relevant for home context). Label IPs (private/lab only, do not publish public IP in shared notes). Label ports for services you found in Task 1.

**Task 3 — DNS in lab context:**
Read `network-tools.md` for `dig` examples, then run a DNS check for a public learning name in lab context, as guided there, and note: name asked, IP returned, time taken, and what that IP means. Do not run DNS checks in a loop or at high rate — one or two checks for learning is enough.

**Task 4 — Web journey in lab:**
Open a lab web page you own (or a legal lab platform page), open browser developer tools, note: request method, status code, and one header. Then write the full journey from Section 4.3 for that page, in your own words, step by step.

**Task 5 — Port audit, own machine only:**
For each listening port from Task 1, write the five audit questions from Section 3.2:
1. What service?
2. Should it run?
3. Localhost-only or exposed? Why?
4. Patched/updated in lab terms?
5. Fix idea if it should not run or should be localhost-only?
Save this as a short audit note, with date, in your lab notes folder. Do not publish private details without thinking first.

**Task 6 — Explain a network in 3 minutes:**
Record yourself explaining: "How does a computer find a website, connect, and show a page, in my lab?" Use the journey from Section 4.3, in simple English, without reading notes word for word. That recording is also English practice.

---

## 9. Common mistakes — and how to fix them

- **Mistake: Confusing IP and port.**
  Fix: IP = which house. Port = which door/service in that house. You need both to reach a service: IP + port together, like `127.0.0.1:5432`.

- **Mistake: Thinking open port = vulnerable, automatically.**
  Fix: Open = service present. Next: what service, what version, should it be open, to whom, patched? Those questions decide if it is a finding in lab terms.

- **Mistake: Thinking localhost-only means "no service".**
  Fix: Localhost-only means service is running, but only reachable from same computer. That is often good design for databases and internal services in lab. Check: is localhost-only intentional? If yes, it is safer, not missing.

- **Mistake: Scanning networks you do not own to "learn networking".**
  Fix: Lab only, always. Learning networking does not require scanning others. Your own machine + private lab VMs give you all the practice you need for Phase 2.

- **Mistake: Memorising OSI layers before understanding TCP/IP journey.**
  Fix: Learn journey first (DNS -> TCP -> HTTP), then TCP/IP 4-layer model, then OSI as reference when you meet it in docs. Journey first, layers second.

- **Mistake: Ignoring DNS when a website fails, and blaming the web server immediately.**
  Fix: Debug in journey order: DNS? Connection? TLS? HTTP status? Content? Each error type points to a different layer, and a different fix.

- **Mistake: Sharing public IP or lab details publicly without thinking.**
  Fix: Private/lab IPs in private notes are fine for learning. Public IP, lab server details, or any report with private hostnames should be reviewed before publishing, and private details removed if not needed for the lesson.

---

## 10. Checklist — can you move to Network Tools?

Do not move to `network-tools.md` until you can check these boxes honestly (tools file is next, but basics must be solid first):

- [ ] I can explain IP, private vs public, localhost, port, router, switch, DNS, packet, in simple English, without reading definitions word for word.
- [ ] I can draw my lab network with boxes, IPs (private/lab only), and ports, and explain it in 3 minutes.
- [ ] I ran `ip a`, `hostname -I`, `ss -tln` on my own machine, and I can explain each output, including localhost-only vs exposed.
- [ ] I can walk through the full website journey (DNS -> TCP -> TLS -> HTTP request -> server -> response -> render) step by step, in order.
- [ ] I can explain TCP vs UDP in simple words, with one example use for each.
- [ ] I can explain DNS, DHCP, firewall, NAT, VPN in simple words, with one design note for each (why it exists, what risk it addresses).
- [ ] I completed the port audit note for my own machine, with the five audit questions per port.
- [ ] I recorded a 3-minute explanation of "How a website loads in my lab" in simple English.

If a box is unchecked, that topic goes back through the Learning Algorithm from `01-roadmap/how-to-learn.md`, with lab tasks from Section 8. That is structured, not slow.

**Next file:** `network-tools.md` — ping, ip, ss, dig, nslookup, traceroute concept, curl, Wireshark lab concept, each with purpose, lab example on your own machine only, output meaning line by line, mistakes, and network audit algorithm. No shortcuts, lab only.

---

*Educational use only. All practice must be on systems you own or have written permission to test. Never scan or test networks, websites, or computers you do not own.*
