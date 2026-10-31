# Networks: Network Basics

This is the map everything else hangs off. Web, tools, Active Directory, lab work — all of it assumes you already know how two machines find each other, talk, and decide what to trust. If you can't draw your own lab network from memory, you don't have the map yet. Tools won't fix that.

One rule covers the whole file: your own machine, your own private lab, nothing else. No scanning, sweeping, or probing networks you don't own. Your Kali box and a lab VM or two give you everything you need here.

## The picture to keep in your head

A network is a postal system. The analogy holds up surprisingly far, so use it until it breaks.

An IP address is a house address. `192.168.1.10` is a private house on a home or lab network. `127.0.0.1` is your own house, this machine only — traffic there never leaves the box.

A port is a door in that house, for one service. Port 80 is the plain web door, 443 the encrypted web door, 22 SSH, 5432 PostgreSQL. A machine can have many doors. Some are open because a service is listening, some are shut because nothing is there. The interesting question is never just "which doors are open?" It's "which doors should be open, to whom, and why?"

The router is the post office at the edge. It moves traffic between your local network and other networks. The switch is the sorter inside the building, delivering traffic to the right machine on the same local network without sending it outside.

DNS is the phone book. You type `example.com`, DNS returns an IP, and now your machine knows where to send the packet. A packet is one envelope. Data gets split into packets, sent one by one, and reassembled at the other end. TCP resends a lost envelope. UDP doesn't bother — more on that below.

Every security question in this phase reduces to that picture: what machines exist in this lab, what doors are open on each, who is allowed through each door, and which doors should be localhost-only or closed entirely. If you can't draw that, stop and draw it.

## IP addresses

IPv4 is the form you'll see first: four numbers, dots between them, each 0 to 255, like `192.168.1.10`. IPv6 is the longer form. You'll mostly meet it as `::1`, the IPv6 localhost, and as `[::1]:5432` in `ss` output — same meaning as `127.0.0.1:5432`, localhost only.

Private ranges live inside home, lab, and office networks and aren't directly reachable from the internet:

- `10.0.0.0` to `10.255.255.255` — large private networks and labs
- `172.16.0.0` to `172.31.255.255` — medium private networks
- `192.168.0.0` to `192.168.255.255` — home networks and small labs

A public IP sits on the outside of your router, facing the internet. The split between private inside and public outside is a deliberate design: inside machines aren't exposed unless a port is deliberately forwarded. Forwarding should be a conscious, documented decision, never an accident.

Localhost deserves emphasis because beginners keep misreading it. `127.0.0.1` and `::1` always mean this computer itself. A service bound to localhost can only be reached from that same machine. That's why a database on `127.0.0.1:5432` is a safer default than one on every interface — the door exists, but only you can walk through it.

Check your own setup:

```bash
ip a
hostname -I
```

`ip a` shows every interface and its addresses. `lo` is loopback, localhost. `eth0` or `wlan0` is usually your wired or wireless interface, though names vary. `hostname -I` gives the short version, one line. Look for `127.0.0.1` on `lo` and a private address on your main interface. Treat even private lab addresses as something you don't paste into public notes without thinking.

### Subnets, without the maths drill

You'll see notation like `192.168.1.10/24`. The `/24` is CIDR notation — it says the first 24 bits identify the network, the rest identify machines inside it. In a `/24`, machines usually share the first three numbers, like `192.168.1.x`, with the last number changing per machine: `.1` might be the router, `.10` your Kali box, `.20` a lab server.

A smaller number after the slash means a bigger network with more machines. A bigger number means a smaller network. You don't need to hand-calculate subnets perfectly yet. You need the distinction it encodes: same local network versus a different network reached via the router. That decides whether traffic stays local or goes through the gateway.

For lab design, small and private is the right default. A `/24` private lab network holding only your lab VMs is simple and contained. Don't park vulnerable lab VMs on a large shared network, or on a network with real machines you don't own.

## Ports and listening

A port is a number, 0 to 65535, naming one service on a machine. IP gets you to the house, port gets you to the door.

The ones worth memorising now: 21 FTP (old, plain, insecure by design), 22 SSH, 23 Telnet (old, don't use it for real work), 25 SMTP, 53 DNS, 80 HTTP, 110 POP3, 143 IMAP, 443 HTTPS, 3306 MySQL, 5432 PostgreSQL. The rest you look up when you meet them. That's the professional habit — common ports in memory, everything else in docs.

Four words get confused constantly, so get them straight:

- **Listening** (from `ss` on your own machine) means a service is waiting on that port. `127.0.0.1:5432` in LISTEN state is a database waiting on localhost.
- **Open** (from a check against a lab machine you own) means a service answered on that port. Open means present, not vulnerable. The follow-up questions do the real work: what service, what version, should it be open, to whom?
- **Closed** means the machine answered "no service here." The machine is reachable, the door is shut.
- **Filtered** means no clear answer, usually a firewall dropping the probe silently. Filtered proves neither open nor closed — it proves you can't tell from outside, which is often the firewall doing its job.

Run this audit on every listening port on your own machine, and it will teach you more than any port list:

What service is this? Should it be running at all in this lab? Should it listen on localhost only, or on all interfaces, and why? Is it patched? If it shouldn't be listening, or shouldn't be exposed, the fix is concrete: stop the service, bind it to localhost, or firewall it.

### TCP versus UDP

TCP is a phone call. Connect first, talk, confirm you heard, resend anything lost. The handshake is SYN, then SYN-ACK, then ACK, then data. Web, email, SSH, and databases ride on TCP because every byte has to arrive, in order.

UDP is a postcard. Send it, hope it arrives, no confirmation per packet. DNS queries, streaming, games, and voice ride on UDP because speed matters more than perfect delivery, or the application handles retries itself.

Watch out for one habit: UDP has no handshake, so UDP services can be abused for amplification in real attacks. You don't practise that. You learn to recognise UDP services in lab and ask the same audit questions — should this run, should it be exposed.

`ss -tln` shows TCP listeners, `ss -uln` shows UDP listeners. Both need the same treatment.

## DNS, DHCP, and the journey of a web request

DNS turns names into IPs. You type `example.com`, your machine asks a DNS server, the server answers with an IP — `93.184.216.34` in the classic example — and the connection goes to that IP on port 80 or 443.

The record types you'll meet first: A maps a name to an IPv4 address, AAAA to IPv6, CNAME maps a name to another name as an alias, MX points to a mail server, TXT carries text data often used for ownership verification. Learn A, AAAA, and CNAME cold; pick up MX and TXT when they show up in tool output.

DNS was built for convenience, not security, and that history matters. Spoofing and poisoning — tricking a machine into accepting the wrong IP for a name — are real concepts, and DNSSEC (signed answers) and DNS over HTTPS (encrypted queries) exist as defences. Learn normal DNS first, then why those defences exist. Don't go testing real DNS servers.

DHCP is the automatic part. When a machine joins a network, a DHCP server hands it an IP, a subnet mask, a gateway address, and a DNS server address, for a limited lease time. That's why your Kali VM usually has a private IP without you typing anything — the router handed it one at startup. The flip side, worth knowing early: a rogue DHCP server on a network you don't own could hand out a wrong gateway or DNS server and redirect traffic. Another reason to only join networks you trust, and only run a DHCP server in your own private lab.

Now the journey itself. This is the single most useful mental model in the file, because it doubles as a debugging order. When you open a page:

1. **DNS lookup.** Browser asks for the IP of `example.com`. If DNS fails, the browser says it can't find the site. That's a DNS problem, not a web server problem. Learn to tell them apart.
2. **TCP connection.** Browser connects to that IP on port 443 for HTTPS, 80 for HTTP. If it fails, the server is down, the port is closed or filtered, or the network is broken. Different error, different cause.
3. **TLS handshake, HTTPS only.** Browser and server agree encryption, the server presents its certificate, the browser checks it matches the name, is trusted, and isn't expired. Certificate warnings on real sites are not to be clicked through. In lab, learn what a valid versus invalid certificate looks like on a site you own.
4. **HTTP request.** Method (GET for a page), path, headers, cookies if any.
5. **Server processing.** The server decides what was asked for, may talk to a database, may check a session, then picks a status: 200, 404, 403, 500.
6. **HTTP response.** Status, headers, body. The browser renders it.
7. **Repeat for every resource.** Images, stylesheets, scripts — each is another request through the same steps. That's why developer tools show dozens of requests for one page.

When a page won't load, debug in that order instead of guessing: DNS, then connection, then TLS, then HTTP status, then content. That order saves hours, and it's exactly how experienced people troubleshoot.

## The models: TCP/IP first, OSI as reference

Beginners get told to memorise the seven OSI layers on day one. That's a shortcut to forgetting them. Learn the idea first, the names second, details when a tool forces you to.

The TCP/IP model, top to bottom, is the one the real internet actually uses:

1. **Application** — what the program wants: HTTP, DNS, SSH, SMTP.
2. **Transport** — how delivery works: TCP or UDP, and which port.
3. **Internet** — how data crosses networks: IP addresses, routing.
4. **Link** — how data moves on one local link: Ethernet, Wi-Fi, MAC addresses.

The OSI model has seven layers: Application, Presentation, Session, Transport, Network, Data Link, Physical. You'll meet those names in docs and in Wireshark. Keep them as reference, not as a memorisation project. Journey first, TCP/IP second, OSI when it shows up.

A MAC address is the hardware address burned into a network card — six pairs of hex digits like `aa:bb:cc:dd:ee:ff` in example form. It's used on a local link to deliver data to the right physical device, after IP has routed it to the right network. ARP is how a machine asks on its local network: "Who has `192.168.1.10`? Tell me your MAC." That's a local question, not an internet one.

One caution that saves bad design later: MAC addresses can be changed in software on your own lab machine, so MAC alone is a weak identity. Don't rely on MAC filtering as a real defence, and don't practise MAC spoofing on networks you don't own.

## Firewall, NAT, VPN

A firewall is a gate with rules: what traffic is allowed in, allowed out, or blocked. A good default is default-deny — block everything, then allow one thing at a time with a note saying why. Default-allow is weaker because every new problem is allowed until someone gets around to blocking it. When you audit a lab firewall, ask whether it's default-deny and whether every allow rule is documented and needed.

NAT lets many private machines share one public IP. Inside machines have private addresses; the router rewrites the source to its public IP on the way out and reverses it on the way back. NAT hides inside addresses by default, but it is not a firewall by itself. A forwarded port becomes reachable from outside on purpose — that forwarding needs to be deliberate and checked, never accidental.

A VPN is an encrypted tunnel between your machine and another network, across the public internet. Outsiders can see a tunnel exists but not the traffic inside it, if the VPN is designed right. Companies use VPNs for remote workers; lab platforms sometimes use them to connect your VM to their lab network. A VPN is a tool with a design, not automatic safety — a weak or malicious VPN fails like anything else.

Lab design, in one line: private lab network, NAT and firewall by default, VPN only when a trusted lab platform requires it, and never a vulnerable lab VM sitting directly on the internet.

## How to understand any new lab network

Take a new lab network — say your Kali VM and one lab server VM on a private network — and work it through in this order.

Start with your own machine: `ip a`, `hostname -I`. Note your private IP, your interfaces, localhost. Draw the map: boxes for each machine and the gateway, lines for the connections, private IPs on the boxes, and notes on what traffic flows where. Check listening services on your own machine with `ss -tln` and run the port audit on each one: localhost-only or exposed, needed or not.

Then check name resolution for a lab name with `dig`, and trace one simple journey through the map — your Kali box asking the lab server for a web page — step by step: DNS, TCP, HTTP request, response. Finally, check the gate: what's allowed in, what's allowed out, what's blocked by default, and is any lab service forwarded outside? If you can't answer a piece, that's a gap to study, not a failure — write it down as a question.

Then explain the whole thing in three minutes, out loud, without notes: how many machines, what IPs, what services are listening and why, and the path a web request takes. Anything you can't explain goes back to the start. That explanation is the real test of this file, and it's good English practice at the same time.

## Working through it on your own machine

Start with the three commands and write down what you actually see: interfaces, private IP, localhost, and each listening port with a localhost-only versus exposed note. Draw the lab network on paper, boxes and IPs and ports, and keep public IPs out of any shared notes.

Read the `dig` section of the network-tools file, then run one DNS check in lab context and note the name asked, the IP returned, and what that IP means. Open a lab page you own, open developer tools, and note the method, the status code, and one header — then write the full journey for that page in your own words. Run the port audit for your own machine, five questions per port, and save it as a short dated note in your lab folder. Then record a three-minute explanation of how a website loads in your lab, without reading notes word for word.

Two habits to carry out of this file. First, an open port is not automatically a vulnerability — it's a service, and the questions after it decide whether it's a finding. Second, localhost-only is not the same as "no service." It's a service deliberately reachable only from the same machine, which for databases and internal services is usually good design. Check whether it's intentional. If it is, it's safer, not missing.

Next: network-tools — `ping`, `ip`, `ss`, `dig`, `curl`, and the Wireshark concept, each against your own machine and lab only.

*Educational use only. All practice on systems you own or have written permission to test.*
