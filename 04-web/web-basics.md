# 04 — Web: Web Basics, Fully Explained

## What this file is

This is Phase 3, part 1. You already know computers, Linux, and networks.
Now you learn how the web works on top of those networks — because most
red-team work, and most real systems you will one day defend or test with
permission, are web systems.

If you can explain this file in simple English, read a URL piece by piece,
read an HTTP request and response, and explain what a cookie and a session
are, you have the map for the next file, OWASP, and for all web testing later.
Without this map, web tools are just noise.

No shortcuts here. Each concept gets: simple explanation, practical design
notes, step-by-step algorithm/logic, a lab example on your own machine only,
and common mistakes.

**Safety rule for this whole file:**
All examples are on your own machine, or your own private lab, only.
- Use `http://127.0.0.1:8000` and `example.com` as examples, never a real
  website you do not own.
- Never test, probe, or "try" anything on a real website, school system, or
  company system to learn — that is unauthorised testing, not learning.
- Never use a real username, real password, or real session value in any
  example. Use placeholders such as `<your-username>` and
  `<your-test-password>`.
- A lab server on your own machine is for learning how the web works. It is
  not a target, and it is not a tool against anyone else.

---

## 1. The web, in one picture

Think of the web as a restaurant, with a clear order of work:

- **Browser = the customer.**
  It asks for things, shows the answer, and remembers small notes for you.
  Examples: Firefox, Chromium, and other browsers on your own machine.

- **Server = the kitchen.**
  It receives the order, prepares the answer, and sends it back. A server
  is just a program running on a computer, listening on a port.

- **Website / web app = the menu and the kitchen rules.**
  The menu says what you can order. The kitchen rules say who can order
  what, what is checked before cooking, and what is sent back.

- **Network = the road between customer and kitchen.**
  This is the network you learned in Phase 2: IP addresses, ports, DNS,
  and packets. The web rides on top of it; it does not replace it.

- **HTTP = the language of the order.**
  HTTP (HyperText Transfer Protocol) is the set of rules for how the
  browser asks and how the server answers. HTTPS is the same language,
  but with a sealed, private envelope (see Section 6).

**Practical design note:**
Every web security question starts with this picture: Who is asking?
What exactly are they asking for? What does the server check before it
answers? What does it send back, and to whom? If you cannot answer those
four questions for a lab page, you cannot test it safely or explain it.

---

## 2. A URL, piece by piece

A URL (Uniform Resource Locator) is an address for one thing on the web.
Read it left to right, in this order:

```
https://example.com:443/shop/item?id=42#details
```

| Piece | Meaning | Example |
|---|---|---|
| Scheme | Which language/rules to use | `https` |
| Host | Which computer/server, by name | `example.com` |
| Port | Which door on that server | `443` (the usual door for HTTPS; often hidden) |
| Path | Which thing on that server | `/shop/item` |
| Query | Extra details sent with the ask | `?id=42` |
| Fragment | Which part of the page to jump to, handled by the browser only | `#details` |

**Step-by-step algorithm: how to read any URL**

1. Find the scheme, before `://`. It tells you the rules: `http` or `https`.
2. Find the host, after `://` and before the next `/` or `:`. This is the
   server's name. Your computer turns this name into an IP address with DNS
   (Phase 2).
3. Find the port, if there is a `:` after the host. If there is none, use
   the default: 80 for HTTP, 443 for HTTPS.
4. Find the path, from the first `/` after the host to the next `?` or `#`
   or the end. This is the thing being asked for.
5. Find the query, after `?`. It is pairs of `name=value`, joined by `&`.
   Example: `?id=42&sort=price` means id is 42 and sort is price.
6. Find the fragment, after `#`, if there is one. The browser uses it
   locally; it is not sent to the server as part of the ask.

**Lab example — read this one out loud:**
`http://127.0.0.1:8000/notes?page=2`
- Scheme: `http`. Host: `127.0.0.1` — this computer only, your lab.
- Port: `8000`. Path: `/notes`. Query: `page=2`.
- Meaning: "On this computer, door 8000, show me the notes thing, page 2."

**Common mistakes:**
- Thinking the whole URL is one name. It is six pieces, and each piece can
  be wrong in a different way.
- Thinking the fragment is sent to the server. It is not; the browser
  keeps it.
- Putting secret values in a URL query. Queries can be saved in browser
  history and server logs, so never put a password or token in a URL.
  Use a safe lab placeholder if you must show an example.

---

## 3. HTTP: the request and the response

Every web action is one round trip: a request, then a response.

### 3.1 The request

A request has four parts, in order:

1. **Method** — what kind of action you want. The main ones:
   - `GET` — "Show me this." Used to read a page, image, or data. It
     should not change anything on the server.
   - `POST` — "Take this data and do something with it." Used for forms,
     logins, and creating things.
   - `PUT` — "Replace this thing with this new version."
   - `PATCH` — "Change part of this thing."
   - `DELETE` — "Remove this thing."
   - `HEAD` — like `GET`, but "send only the headers, not the body."
   - `OPTIONS` — "What methods and rules does this thing allow?"
2. **Path** — the thing you are asking about, from the URL.
3. **Headers** — extra notes about the ask (see Section 4).
4. **Body** — the data itself, mainly for `POST`, `PUT`, and `PATCH`.
   A `GET` request normally has no body.

A simple `GET` request, written out:

```
GET /notes?page=2 HTTP/1.1
Host: example.com
User-Agent: example-browser
Accept: text/html
```

Read it as: "Using HTTP version 1.1, show me /notes, page 2, on
example.com. I am a browser, and I accept HTML pages."

### 3.2 The response

A response also has four parts, in order:

1. **Status line** — the HTTP version, a status code, and a short reason.
   Example: `HTTP/1.1 200 OK`.
2. **Headers** — extra notes about the answer (see Section 4).
3. **Blank line** — separates headers from body.
4. **Body** — the actual page, image, JSON data, or error message.

A simple response, written out:

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 48

<html><body><h1>Notes, page 2</h1></body></html>
```

### 3.3 Status codes, grouped by first digit

Do not memorise every code. Learn the groups first, then the common ones:

- **2xx — Success.** The ask worked.
  - `200 OK` — worked, here is the answer.
  - `201 Created` — worked, and a new thing was created.
  - `204 No Content` — worked, but there is nothing to send back.
- **3xx — Redirect.** "Not here — go there instead."
  - `301 Moved Permanently` — this thing moved for good.
  - `302 Found` — this thing moved for now.
  - `304 Not Modified` — you already have the newest version; use your copy.
- **4xx — Client error.** Something about the ask was wrong.
  - `400 Bad Request` — the ask was broken or unclear.
  - `401 Unauthorized` — you must log in first. (The name is confusing:
    it really means "not authenticated yet".)
  - `403 Forbidden` — you are logged in, but you are not allowed this.
  - `404 Not Found` — no such thing on this server.
  - `429 Too Many Requests` — you asked too fast; slow down.
- **5xx — Server error.** The server failed while trying.
  - `500 Internal Server Error` — something broke on the server.
  - `502 Bad Gateway` — a server in the middle got a bad answer.
  - `503 Service Unavailable` — the server is busy or down for now.

**Practical design note:**
`401` vs `403` is a classic interview and lab question. Remember it as:
401 = "Who are you? Log in." 403 = "I know who you are. Still no."
A good server never says "404" for a thing that exists but you may not
see — that difference itself can leak information — but that is a design
choice to notice, not a rule to break in a lab.

**Step-by-step algorithm: following one request**

1. The browser builds the request: method, path, headers, and body if any.
2. DNS turns the host name into an IP address (or the host is already
   `127.0.0.1`, this computer).
3. The browser connects to that IP on the right port: 80, 443, or the
   port in the URL, such as 8000 in a lab.
4. The browser sends the request over that connection.
5. The server reads the method and path, checks who you are and what you
   are allowed to do, does the work, and builds a response.
6. The browser reads the status code first, then the headers, then the
   body, and shows or saves the result.
7. If the status is a redirect (3xx), the browser follows the new
   location with a new request, and the steps repeat.

---

## 4. Headers: the notes on the envelope

Headers are `Name: value` lines. They are not the data itself; they are
notes about the data, the asker, and the rules.

Headers you will meet first:

- `Host` — which server name this ask is for. Needed because one server
  can host many names.
- `User-Agent` — which browser or tool is asking. Servers use it to adapt
  a page, never to trust who you are.
- `Accept` — what kind of answer the asker can handle: HTML, JSON, image.
- `Content-Type` — what kind of data is in the body. Examples:
  `text/html` for a page, `application/json` for JSON data,
  `application/x-www-form-urlencoded` for a simple form.
- `Content-Length` — how many bytes are in the body.
- `Cookie` — small notes the browser sends back to the server (Section 5).
- `Set-Cookie` — a note the server asks the browser to store (Section 5).
- `Authorization` — proof of who you are, such as a token, sent with an
  ask. In a lab, use a placeholder value only, never a real token.
- `Location` — where to go next, used with a redirect (3xx).
- `Cache-Control` — rules about saving a copy and reusing it.

**Practical design note:**
Headers are easy to read and easy to change. A server must never trust a
header as proof by itself. `User-Agent` can say anything. `Cookie` values
can be copied. Trust comes from a checked session on the server
(Section 5) and from rules the server enforces, not from a header that
arrives in the ask.

**Common mistakes:**
- Thinking headers are hidden or secret. Anyone who can see the request
  can see its headers. Never put a password in a header in plain HTTP,
  and in a lab use placeholders only.
- Thinking `Content-Type` proves what the data really is. It is only a
  label the sender wrote. The server must still check the data itself.

---

## 5. Cookies and sessions: how the web remembers you

HTTP by itself has no memory. Each request is a new stranger. Cookies and
sessions are how a web app remembers you between requests — and this is
one of the most important ideas in all of web security.

### 5.1 Cookies, simply

A cookie is a small piece of text that a server asks your browser to store,
and that your browser then sends back with later asks to the same site.

Think of it as a coat-check ticket: the shop gives you a ticket, you keep
it, and you show it each time you come back so the shop knows which coat
is yours.

A server sets a cookie with a `Set-Cookie` header:

```
Set-Cookie: session_id=<example-session-value>; Path=/; HttpOnly
```

Then the browser sends it back with later asks:

```
Cookie: session_id=<example-session-value>
```

Cookie rules you must know, in plain words:

- `HttpOnly` — JavaScript in the page cannot read this cookie. This is a
  safety rule: even if a page has a script problem, the script cannot
  steal this cookie value easily.
- `Secure` — send this cookie only over HTTPS, never over plain HTTP.
- `SameSite` — limits when the browser sends this cookie with asks that
  come from another site. It helps stop other sites from using your
  logged-in state without you knowing.
- `Path` and `Expires` / `Max-Age` — where the cookie applies, and how
  long the browser keeps it.

### 5.2 Sessions, simply

A session is the server-side memory. The cookie usually holds only a
random session ID — a ticket number. The real memory lives on the server:
who is logged in, what they are allowed to do, and when the session ends.

**Step-by-step algorithm: a login session, lab only**

1. You open the lab login page. The browser sends a `GET` for the page.
2. You type a lab username and a test password into the form. In a real
   system these are placeholders in this file only: `<your-username>` and
   `<your-test-password>`. Never type a real password into a lab example.
3. The browser sends a `POST` with the form data in the body.
4. The server checks the username and password against its own lab store.
   If they do not match, it answers `401` or a login error page, and it
   creates no session.
5. If they match, the server creates a new session on the server, with a
   fresh, random session ID, and remembers: "This session belongs to this
   lab user, until this time."
6. The server answers with `Set-Cookie: session_id=<example-session-value>`
   and a redirect to the main lab page.
7. For each later ask, the browser sends `Cookie: session_id=...`.
8. The server looks up that session ID in its own memory. If it finds a
   live session, it knows who you are and what you may do. If it finds
   nothing, or an expired session, it treats you as logged out.
9. When you log out, the server deletes that session, and the browser
   drops the cookie. The old ticket number must stop working.

**Practical design note:**
The session ID must be long, random, and impossible to guess, and it must
change after login. If a session ID is short, guessable, or stays the same
before and after login, an attacker in a lab can copy or guess it and
pretend to be that user. That is why the next file, OWASP, treats broken
authentication as a top risk — the session is the key to the whole app.

**Common mistakes:**
- Storing the whole user record, or a password, inside the cookie. A
  cookie lives in the browser and can be read or copied. Store only a
  random ID in the cookie; keep the real record on the server.
- Never expiring a session. A session that lives forever is a key that
  never stops working. Lab sessions should expire, and logout must
  actually delete the session on the server.
- Trusting a cookie value without checking it on the server. If the
  server believes any cookie it receives, anyone can write their own
  cookie and claim to be anyone.

---

## 6. HTTPS: the sealed envelope

HTTP sends the request and response in plain text. Anyone on the road
between browser and server who can see the traffic can read it.

HTTPS is HTTP inside TLS (Transport Layer Security), the sealed envelope:

- **Encryption** — the data is scrambled so only the browser and the
  server can read it.
- **Authentication** — the browser checks the server's certificate, a
  signed identity card, to confirm it is really talking to the server
  named in the URL.
- **Integrity** — if anyone changes the data on the way, the check fails
  and the browser knows.

**Step-by-step logic, simply:**
Browser asks for a secure connection → server shows its certificate →
browser checks that the certificate matches the host name and is signed
by a trusted authority → browser and server agree on secret keys for this
visit → all HTTP traffic for this visit travels inside that encrypted
channel → the same HTTP rules from Section 3 still apply, now in private.

**Practical design note:**
HTTPS protects data on the road. It does not make a web app safe by
itself. A site can have perfect HTTPS and still have a broken login,
broken access control, or an injection flaw — those are app problems, and
they are the subject of the OWASP file. Do not confuse "the road is
sealed" with "the kitchen checks who you are".

**Lab note:**
On your own machine, `http://127.0.0.1:8000` is safe for learning
because the traffic never leaves your own computer. That is the exception
that proves the rule: localhost traffic stays local. For any real network,
even a lab network, prefer HTTPS, and never send a real password over
plain HTTP.

---

## 7. Forms, JSON, and APIs, simply

### 7.1 Forms

A form is a page asking you to fill in fields and send them. A simple form
sends a `POST` with `Content-Type: application/x-www-form-urlencoded`,
which means the fields become `name=value&name=value` in the body.

Example, lab only, with placeholders:

```
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 48

username=<your-username>&password=<your-test-password>
```

### 7.2 JSON

JSON (JavaScript Object Notation) is a simple way to write structured data
as text, so programs can send it to each other:

```json
{
  "username": "<your-username>",
  "page": 2,
  "notes": ["first note", "second note"]
}
```

Rules: names in double quotes, values can be text, numbers, true/false,
null, lists in `[]`, or objects in `{}`. JSON is data, not code — a good
server treats it as data and never runs it as code.

### 7.3 APIs

An API (Application Programming Interface) is a set of URLs and rules
that programs use to talk to each other, instead of humans reading pages.
A web API usually returns JSON instead of HTML.

Example, lab only: `GET /api/notes?page=2` might return JSON with a list
of notes, while `GET /notes?page=2` returns an HTML page with the same
notes for a human. Same data, different shape, different audience.

**Why this matters for your path:**
You already know HTML, CSS, and JavaScript except JS APIs. This section
is the bridge: JS APIs you will learn next — `fetch`, promises, and
`async/await` — are how a page in the browser talks to a web API. Learn
the HTTP shape first, and the JS code will make sense: `fetch` builds a
request, the server answers, and your code reads the status and the JSON.

**Step-by-step algorithm: how a page loads data from an API**

1. The browser loads the HTML page.
2. The page's JavaScript runs and calls the API with `fetch`.
3. `fetch` builds an HTTP request: method, URL, headers, and body if any.
4. The server checks the session, does the work, and answers with a
   status code and a JSON body.
5. The JavaScript reads the status first. On 2xx it reads the JSON; on
   4xx or 5xx it shows a safe error, not the raw server detail.
6. The page updates only the part that changed, without reloading.

---

## 8. How a page loads, end to end

Put Sections 2 to 7 together. This is the full algorithm for one page:

1. You type or click a URL.
2. The browser splits the URL into scheme, host, port, path, query, and
   fragment (Section 2).
3. DNS turns the host name into an IP address (Phase 2), unless the host
   is already `127.0.0.1`.
4. The browser connects to that IP and port. For HTTPS, the TLS sealed
   envelope is set up first (Section 6).
5. The browser sends an HTTP request: method, path, headers, cookies,
   and body if any (Sections 3–5).
6. The server checks the session and permissions, does the work, and
   sends back a status code, headers, and body.
7. If the status is a redirect, go back to step 2 with the new location.
8. The browser reads the HTML body, then asks for the images, styles,
   and scripts that page needs — each one is another HTTP request, with
   the same steps.
9. JavaScript on the page may call APIs for more data (Section 7), each
   one another HTTP request.
10. The browser shows the finished page, and keeps the cookies for the
    next visit to that site.

**Practical design note:**
Notice how many separate requests one "page" really is. A slow or broken
page is often one slow or broken request among many. When you learn to
read requests one by one, in order, you stop guessing about web problems
and start tracing them.

---

## 9. Lab: a web server on your own machine only

You do not need the internet, and you must not test a real site, to see
all of this working.

**Lab setup — own machine only:**

1. Make a small lab folder and one page in it, on your own machine:

```bash
mkdir -p ~/web-lab
cd ~/web-lab
echo "<h1>Hello from my own machine</h1>" > index.html
ls -l
```

2. Start a simple server that listens on localhost only, port 8000:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

Leave that terminal open while you test. It serves only the files in
`~/web-lab`, only to your own computer.

3. In a second terminal, ask for the page and see the full response,
   headers included:

```bash
curl -i http://127.0.0.1:8000/
```

Expected, in simple terms: a `200 OK` status line, headers such as
`Content-Type` and `Content-Length`, a blank line, then your
`<h1>Hello from my own machine</h1>` body.

4. Ask for a thing that does not exist, and watch the status change:

```bash
curl -i http://127.0.0.1:8000/no-such-page
```

Expected: `404 Not Found`. Same server, same steps, different status —
because the path asked for a thing the server does not have.

5. Stop the server with `Ctrl+C` in the first terminal when you are done.
   A lab server left running is a door left open, even on localhost. Close
   it when the lesson ends.

**Check yourself — answer in simple English, without looking back:**
1. Split `https://example.com/shop/item?id=42` into scheme, host, path,
   and query.
2. What is the difference between `401` and `403`?
3. Why does a cookie hold only a session ID, not the whole user record?
4. Why is `http://127.0.0.1:8000` safe for this lab, but plain HTTP to a
   real site is not?
5. A page loads slowly. Which step in Section 8 would you check first,
   and what would you look for?

**Common mistakes in this lab:**
- Starting the server without `--bind 127.0.0.1`, so it listens on every
  network door instead of localhost only. Always bind to localhost for
  a learning lab.
- Serving a folder that holds real personal files. Serve only a small
  lab folder you made for this lesson, with only lab files in it.
- Leaving the server running after the lesson. Stop it with `Ctrl+C`.

---

## 10. What comes next

You now have the web map: URLs, requests, responses, headers, cookies,
sessions, HTTPS, forms, JSON, and APIs.

The next file, `owasp-simple.md`, uses this map to explain the ten most
common web risks in simple words — what each one means, how it happens in
design, and what a safe design does instead. Read it as a defence map
first: every risk there is a design mistake you will learn to recognise
in your own lab apps, long before you ever test anyone else's system,
and only ever with written permission.

**One-line summary to remember:**
The web is a browser asking a server, in HTTP, for a thing named by a URL;
the server checks who you are and what you may do, then answers with a
status, headers, and a body — and cookies and sessions are how it
remembers you between asks.
