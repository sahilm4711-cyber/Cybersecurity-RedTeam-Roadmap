# Networks: Network Tools

The basics file gave you the map. This file gives you the tools that show you the map on your own machine. The bar here is higher than "I know the tool names." It's: run the tool, read the output line by line, and say what it proves and what it does not prove. If you can't do that, you don't understand the result yet, and a real result will mislead you.

Same rule as before: your own machine, your own private lab, small single checks. One `ping` with a count, one `dig` query, one `curl` to a lab service — not a flood, not a sweep, not a loop. Use generic placeholders in notes: `your-user`, `<your-lab-ip>`, `<test-password>`. Never a real username, hostname, or password in a note or screenshot.

## Question first, tool second

A tool doesn't give you an answer. It gives you evidence for a question. Design every check the same way: what do I want to know, which tool answers it with the smallest check, what should I expect to see, what did I actually see, what does it prove, and what is still unknown.

The mapping is simple, and it's worth keeping in your head:

- "What is my own IP?" — `ip a`
- "What's listening on my own machine?" — `ss -tln`
- "Is my own machine reachable from itself?" — `ping -c 2 127.0.0.1`
- "What does DNS say for this lab name?" — `dig <lab-name> +short`
- "What does my lab web service return?" — `curl -i http://127.0.0.1:<lab-port>/`
- "What do the packets actually look like?" — Wireshark, lab only, with a filter

Running a tool because it looks powerful, without a question behind it, is how people end up scanning things they shouldn't. In real work that's unauthorised testing, not learning.

## `ip`: your own network identity

`ip` shows and, if you're not careful, changes network settings on your own machine. For learning, stick to the read-only forms: `ip a`, `ip link`, `ip route`.

Run it at the start of a lab session, after joining a lab network, or when a lab service doesn't respond and you want to check your own setup first.

```bash
ip a
ip link
ip route
```

Reading the output, line by line, on an example:

```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.10/24 brd 192.168.1.255 scope global eth0
```

`2: eth0:` is the interface number and name — `eth0` usually wired, `wlan0` usually wireless, though names vary. `UP, LOWER_UP` means the interface is up and has a link; `DOWN` means it's off. `mtu 1500` is the maximum packet size for the interface — leave it alone. `inet 192.168.1.10/24` is your IPv4 address and subnet, a private lab address in this example. `brd 192.168.1.255` is the broadcast address for that subnet. `scope global` means the address is usable on that network; `scope host` on `lo` means localhost only.

`ip route` tells you where traffic goes. A line like `default via 192.168.1.1 dev eth0` means: to reach the outside, send packets to the gateway `192.168.1.1` through `eth0`.

Check loopback specifically:

```bash
ip a show lo
```

You should see `127.0.0.1/8` and `::1/128` on `lo`. Your machine talking to itself, always present, even with no cable plugged in.

Two confusions to kill early. The interface name is not an IP — `eth0` is not an address. And a private IP like `192.168.1.10` is not your public IP; it only works inside that private network. In notes, write `<your-lab-ip>`, not the real value.

## `ss`: what's listening on your own machine

`ss` shows sockets on your own machine: listening ports, established connections, and, with the right flag, the process behind them. This is the first audit tool, because a listening service is a door, and the address it binds to decides who can use it. Bound to `127.0.0.1`, only your own machine. Bound to `0.0.0.0`, the whole network.

```bash
ss -tln
ss -tlnp
```

The flags: `-t` TCP only, `-l` listening only, `-n` numbers not names (avoids DNS lookups and shows ports clearly), `-p` process, if you have permission to see it in your lab.

Example output:

```
State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
LISTEN 0       4096    127.0.0.1:5432     0.0.0.0:*
LISTEN 0       4096    [::1]:5432         [::]:*
```

`LISTEN` means the service is waiting for connections. `127.0.0.1:5432` is localhost-only IPv4 — safer. `[::1]:5432` is the same idea in IPv6. `0.0.0.0:5432` would mean listening on all IPv4 interfaces — exposed to the network, and worth checking: why, and does it need to be? The port number alone doesn't prove which program it is; `ss -tlnp` shows the process on your own machine. The peer column `0.0.0.0:*` just means any peer may try to connect if the local side allows it. It doesn't mean a connection exists.

Audit your own listening ports in this order: run `ss -tln`, write down each address and port, mark localhost-only (`127.0.0.1`, `::1`) versus exposed (`0.0.0.0`, `::`), and for each exposed port ask whether you need that lab service and who should reach it. If you don't need it, stop it in your own lab. Run `ss -tln` again and confirm the port is gone or now localhost-only. Write a short note — port, address, verdict, action — with placeholders, no real hostname.

A LISTEN line is not evidence of an attack. It just means a service is waiting. And `ss` tells you nothing about remote ports — it only ever shows your own machine. That's a feature, not a limitation, for this kind of audit.

## `ping`: is one lab host reachable

`ping` sends small ICMP echo requests to one host and measures replies. It tests reachability and round-trip time. Nothing more.

Use it one host at a time, with a count: `ping -c 2 127.0.0.1` to test your own stack, or `ping -c 2 <your-lab-ip>` for one lab VM you own. Never a sweep over a range, never a flood, never repeated pings at a public site to "test speed."

```bash
ping -c 2 127.0.0.1
```

```
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.045 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.038 ms

--- 127.0.0.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
```

Reading it: the target is localhost, which should always reply. Each `64 bytes from` line is one reply, with its sequence number. `ttl=64` is time-to-live remaining — it drops by one per router hop, so a high value on localhost is normal. `time=0.045 ms` is the round-trip; localhost is very fast, a lab VM on the same network usually a few milliseconds. `2 transmitted, 2 received, 0% packet loss` means all replies came back.

What it does not prove matters more than what it does. `100% packet loss` does not prove a host is down — many firewalls block ICMP by design, so all it proves is that ICMP replies didn't come back. A reply does not prove a web service is running, or a port is open, or a host is safe. Reachability is not trust.

Always use `-c 2` or `-c 4` for learning. A `ping` without a count runs forever, and that's a sloppy habit to build.

## `dig` and `nslookup`: what does DNS say

`dig` asks DNS for records for one name. `nslookup` does a similar job with simpler output and is available on many systems. The point of both: when a web journey fails, separate a DNS problem from a web problem. Did DNS give an IP, or did DNS fail first?

One query at a time. `dig <your-lab-name> +short` for a lab name, or `dig example.com +short` as a public learning example — not as a target to probe. No loops, no hunting for subdomains, no querying internal names you don't own.

```bash
dig example.com +short
dig example.com A
nslookup example.com
```

With `+short`, you get just the answer — in this example, `93.184.216.34`. Without it, you get sections worth learning to read: QUESTION is what you asked (name, type), ANSWER is what DNS returned (name, TTL, type, value), AUTHORITY names the authoritative nameserver, ADDITIONAL carries extras like the nameserver's own IP. `Query time` tells you how long it took — a first query is slower, a cached one faster. `SERVER` tells you which DNS server answered, often your router or lab DNS in practice. `status: NOERROR` means success, `NXDOMAIN` means the name doesn't exist, `SERVFAIL` means the server failed to answer.

Record types in brief: A is IPv4, AAAA is IPv6, CNAME is an alias to another name, MX is a mail server, TXT is text often used for verification, NS is a nameserver.

A cached answer is not always a fresh one — TTL tells you how long it may be cached, and after that a new query is needed. And a DNS answer proves nothing about whether the website is up. DNS can answer perfectly while the web service is down. That's exactly why you check DNS and the service separately.

## `traceroute`: what path does a packet take

`traceroute` on Linux, `tracert` on Windows, shows the routers a packet passes through to reach one destination, using TTL values. Each hop replies when its TTL expires. It answers "where does it stop?" — if a lab connection fails after hop 2, the problem is near hop 2, not at the destination.

For learning, a 3-hop lab path on paper is more useful than tracing a real site: your machine, then the lab gateway, then the lab VM. Each hop adds time. A `* * *` line means that hop didn't reply to that probe — often because it blocks ICMP, not because the path is broken. The next hop can still reply.

Two things people get wrong: `* * *` does not mean the network is down, and the hops shown are logical, not physical — they can change between runs. It's a path tool for one destination at a time, not a way to map a network.

## `curl`: what does a lab web service actually return

`curl` makes an HTTP request and shows the response: headers, status code, body. It's a browser without the visual page. A browser hides details; `curl -i` shows them, which is how you learn HTTP and how you test a lab web service you built yourself.

Only against your own lab service, or a service you have written permission to test. If you have a simple lab page on localhost, say port 8000:

```bash
curl -i http://127.0.0.1:8000/
```

Example output:

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 123

<html>...</html>
```

`HTTP/1.1 200 OK` is protocol, status code, reason — 200 means success. `Content-Type: text/html` says the body is HTML; other types you'll meet are `application/json` and `text/plain`. `Content-Length: 123` is the body size in bytes. The blank line ends the headers and starts the body, which is the actual page content.

The other codes you'll meet in lab: 301 and 302 are redirects, go to another URL; 404 is not found, that path doesn't exist on the service; 403 is forbidden, you're not allowed to see it; 500 is a server error, the service failed internally.

A page coming back proves the service responded. It does not prove the service is secure. And a small discipline point: never send real passwords, tokens, or data with `curl` in a public note or screenshot. Test data only, placeholders like `<test-password>`. And never download a script from the internet and run it blind — read it first, always.

## `arp`: how a lab machine finds a neighbour

`arp` shows the ARP table: which IPs on your local network map to which MAC addresses, on your own machine. It's the link between IP (Layer 3) and MAC (Layer 2). When your machine wants to send to a neighbour on the same subnet, it needs that neighbour's MAC, and ARP is how it asks.

Read-only, on your own machine:

```bash
ip neigh
arp -a
```

Example:

```
192.168.1.1 dev eth0 lladdr aa:bb:cc:dd:ee:ff REACHABLE
```

That's the neighbour IP — often the gateway in lab — its MAC in placeholder form, and its state: REACHABLE means the entry is fresh, STALE means it's old and will be re-checked on next use, FAILED means it couldn't be reached.

ARP spoofing and poisoning are attacks, not learning checks — don't do them. And remember ARP only works on the same local subnet. Across routers, the gateway's MAC is used instead.

## Wireshark: what packets actually look like

Wireshark is a packet analyser. It captures packets on an interface in your own lab and shows them decoded, layer by layer: Ethernet, IP, TCP or UDP, HTTP, DNS. `ping`, `dig`, and `curl` give you summaries; Wireshark shows the actual packets behind them. Seeing one DNS query packet, one TCP handshake, and one HTTP request packet, once, in lab, makes the whole network map concrete.

The constraints are strict, and they're the point: your own private lab only, traffic from your own machines, a filter to keep the capture small — `dns` for DNS only, `http` for HTTP only, `ip.addr == <your-lab-ip>` for one lab host. Never a shared, school, company, or public network. Never other people's traffic. Never passwords or tokens, even in lab — test data only, stop the capture as soon as you have the example.

A sensible first run: start a lab service on your own machine with test data only, start Wireshark on the lab interface with a filter like `dns` or `tcp.port == <lab-port>`, run one small check — one `dig`, one `curl` to localhost — then stop immediately. Click one DNS packet and read source IP, destination IP, query name, query type. Click one TCP packet and read source port, destination port, flags (SYN, ACK). Click one HTTP packet and read method, path, status code. Then close Wireshark. Don't save a capture with real data, and don't publish captures at all — summarise the learning in words, with placeholders.

What Wireshark does not do: it doesn't hack a network, it only shows packets you're allowed to capture; it doesn't decrypt HTTPS by magic — you'll see TLS handshake packets, not plaintext bodies, unless you set up an advanced lab with your own keys; and it doesn't prove a network is secure, only what packets looked like in that capture.

The classic beginner error is capturing without a filter on a busy network and drowning in packets. Always filter first.

## Putting it together: one audit of your own machine

Run this once, slowly, on your own machine only, after you've practised each tool separately.

Start with the question: what is my own network setup today? Run `ip a` and note interface, private IP (as `<your-lab-ip>` in public notes), and the gateway from `ip route`. Run `ss -tln`, note each listening port and whether it's localhost-only or exposed, and for each exposed port decide whether you need that lab service — if not, stop it in lab. Run `ping -c 2 127.0.0.1` and confirm 0% loss for localhost. Run one `dig` query for a lab name if you have one, otherwise one `dig example.com +short` as a learning example. If you have a lab web service, run `curl -i` against it once and note status code and content type. Run `ip neigh` and note the gateway and its state. Optionally, one small filtered Wireshark capture in lab, then stop, and write what you saw in words.

Then review your notes and ask what's still unknown and what the next question is. Clean up: stop lab services you don't need, close Wireshark, and make sure no real IPs, hostnames, or passwords are in anything you might share.

A good audit is boring in the best way — small checks, clear notes, no surprises. A bad audit is a flood of scans with no question, no notes, and no understanding. Later red-team work is built on the boring version, always with written permission, in a lab or an authorised scope, never on other people's networks.

That discipline carries straight into the next phase: web basics and OWASP, where you'll use `curl` and DNS knowledge against your own lab web service only.

*All examples are for your own machine or your own private lab only. Test data only. Never publish a real username, hostname, password, or real IP in a note or public file.*
