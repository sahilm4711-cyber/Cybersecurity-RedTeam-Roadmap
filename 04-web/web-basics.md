# Web: Web Basics

You've got computers, Linux, and networks. Now the web, which rides on top of all three. Most red-team work, and most real systems you'll one day defend or test with permission, are web systems. So this map matters: if you can read a URL piece by piece, read an HTTP request and response, and explain cookies and sessions, you have what the OWASP file builds on. Without it, web tools are just noise.

Same rule as the networks files: your own machine, your own private lab. Use `http://127.0.0.1:8000` and `example.com` as examples, never a real site you don't own. Never a real username, password, or session value in an example — placeholders like `your-user` and `<test-password>` only. A lab server on your own machine is for learning how the web works, not a tool against anyone else.

## The picture

Think of the web as a restaurant. The browser is the customer — it asks for things, shows the answer, remembers small notes for you. The server is the kitchen — a program on a computer, listening on a port, that receives the order, prepares the answer, and sends it back. The website is the menu plus the kitchen rules: what you can order, what's checked before cooking, what's sent back. The network is the road between customer and kitchen — IPs, ports, DNS, packets, everything from Phase 2. HTTP is the language of the order.

Every web security question starts with four questions about that picture: who is asking, what exactly are they asking for, what does the server check before it answers, and what does it send back, to whom? If you can't answer those for a lab page, you can't test it or explain it.

## Reading a URL

A URL is an address for one thing on the web. Read it left to right:

```
https://example.com:443/shop/item?id=42#details
```

Scheme first — `https` — which rules to use. Then host — `example.com` — which server, by name; your machine turns that name into an IP with DNS. Then port, if one is shown after a colon — 443 here, the usual HTTPS door, often hidden because it's the default. Then path — `/shop/item` — which thing on that server. Then query — `?id=42` — extra details, pairs of `name=value` joined by `&`, like `?id=42&sort=price`. Then fragment — `#details` — which part of the page to jump to, handled by the browser only and never sent to the server.

Read this one out loud: `http://127.0.0.1:8000/notes?page=2`. Scheme `http`, host `127.0.0.1` — this computer only, your lab — port 8000, path `/notes`, query `page=2`. "On this computer, door 8000, show me notes, page 2."

Two things beginners get wrong here. The fragment is not sent to the server, so nothing on the server can act on it. And query strings are a bad place for secrets — they end up in browser history and server logs, so never put a password or token in a URL. A placeholder in an example is fine; a real value is not.

## HTTP: request, then response

Every web action is one round trip: a request, then a response.

A request has four parts. Method — what kind of action: GET means "show me this" and should not change anything on the server; POST means "take this data and do something with it," used for forms, logins, and creating things; PUT replaces a thing, PATCH changes part of one, DELETE removes one, HEAD is GET but headers only, OPTIONS asks what a thing allows. Path — the thing being asked about, from the URL. Headers — extra notes about the ask. Body — the data itself, mainly for POST, PUT, and PATCH; a GET normally has no body.

A simple GET, written out:

```
GET /notes?page=2 HTTP/1.1
Host: example.com
User-Agent: example-browser
Accept: text/html
```

"Using HTTP 1.1, show me /notes, page 2, on example.com. I'm a browser, and I accept HTML."

A response has four parts too: status line, headers, a blank line, body.

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 48

<html><body><h1>Notes, page 2</h1></body></html>
```

Don't memorise every status code. Learn the groups, then the common ones. 2xx means success — 200 OK, 201 Created, 204 No Content. 3xx means redirect, "not here, go there" — 301 moved permanently, 302 moved for now, 304 not modified, use your cached copy. 4xx means the ask was wrong somehow — 400 bad request, 401 unauthorised, 403 forbidden, 404 not found, 429 too many requests, slow down. 5xx means the server failed while trying — 500 internal error, 502 bad gateway, 503 unavailable for now.

The 401 versus 403 distinction is a classic lab and interview question, and it's worth getting clean: 401 means "who are you? Log in first." The name is confusing — it really means not authenticated yet. 403 means "I know who you are. Still no." One is about identity, the other about permission.

Follow one request end to end and the rest of the web gets easier: the browser builds the request; DNS turns the host into an IP, unless it's already `127.0.0.1`; the browser connects on the right port — 80, 443, or the lab port like 8000; it sends the request; the server checks who you are and what you're allowed to do, does the work, and builds a response; the browser reads the status first, then headers, then body; and if the status is a redirect, the browser follows the new location and the steps repeat.

## Headers: notes on the envelope

Headers are `Name: value` lines. They're not the data; they're notes about the data, the asker, and the rules. The ones you'll meet first:

Host, which server name the ask is for — needed because one server can host many names. User-Agent, which browser or tool is asking — servers adapt pages to it, but never trust it as identity. Accept, what kind of answer the asker can handle: HTML, JSON, image. Content-Type, what kind of data is in the body — `text/html` for a page, `application/json` for JSON, `application/x-www-form-urlencoded` for a simple form. Content-Length, the body size in bytes. Cookie and Set-Cookie, the memory mechanism in the next section. Authorization, proof of who you are, like a token — lab placeholders only, never a real token. Location, where to go next on a redirect. Cache-Control, rules about saving a copy and reusing it.

Headers are easy to read and easy to change, and that's the lesson: a server must never trust a header as proof by itself. User-Agent can say anything, cookie values can be copied. Trust comes from a checked session on the server, not from a header that arrived in the ask. Content-Type is only a label the sender wrote, so the server still has to check the data itself. Headers aren't secret — anyone who can see the request can see them.

## Cookies and sessions: how the web remembers you

HTTP on its own has no memory. Every request is a new stranger. Cookies and sessions are how a web app remembers you between requests, and this is one of the most important ideas in web security.

A cookie is a small piece of text the server asks your browser to store, which the browser then sends back with later asks to the same site. Coat-check ticket: the shop gives you a ticket, you keep it, you show it each time you come back so the shop knows which coat is yours.

The server sets one with a `Set-Cookie` header:

```
Set-Cookie: session_id=<example-session-value>; Path=/; HttpOnly
```

The browser sends it back:

```
Cookie: session_id=<example-session-value>
```

Four cookie rules to know in plain words. HttpOnly means JavaScript in the page cannot read the cookie — a safety rule, so a script problem in the page can't easily steal it. Secure means send it only over HTTPS, never plain HTTP. SameSite limits when the browser sends it with asks coming from another site, which helps stop other sites from riding your logged-in state. Path and Expires or Max-Age say where it applies and how long the browser keeps it.

The session is the server-side memory. The cookie usually holds only a random session ID — a ticket number. The real record lives on the server: who is logged in, what they're allowed to do, when the session ends.

A lab login, step by step: you open the login page with a GET; you type a lab username and a test password into the form — in this file, `your-user` and `<test-password>`; the browser sends a POST with the form data in the body; the server checks the pair against its own lab store, and if they don't match it answers with a login error and creates no session; if they match, it creates a new session with a fresh, random session ID and remembers who it belongs to and until when; it answers with `Set-Cookie` and a redirect to the main page; on every later ask the browser sends the cookie back; the server looks the session ID up in its own memory, and if it finds a live session it knows who you are and what you may do — if it finds nothing or an expired session, you're logged out; and when you log out, the server deletes the session and the browser drops the cookie, so the old ticket number stops working.

That design has sharp edges worth remembering. The session ID must be long, random, impossible to guess, and it must change after login. If it's short, guessable, or stays the same before and after login, someone in a lab can copy or guess it and pretend to be that user. That's why broken authentication sits near the top of the OWASP list — the session is the key to the whole app.

And three design errors to avoid in your own lab apps. Don't store the whole user record, or a password, inside a cookie — a cookie lives in the browser and can be read or copied, so store only a random ID and keep the record on the server. Don't let sessions live forever — a session that never expires is a key that never stops working, and logout must actually delete the session on the server, not just hide the page. And don't trust a cookie value without checking it on the server — if the server believes any cookie it receives, anyone can write their own and claim to be anyone.

## HTTPS: the sealed envelope

Plain HTTP sends requests and responses as readable text. Anyone on the road between browser and server who can see the traffic can read it.

HTTPS is HTTP inside TLS, the sealed envelope, and it gives three things: encryption, so only browser and server can read the data; authentication, so the browser checks the server's certificate — a signed identity card — and confirms it's really talking to the server named in the URL; and integrity, so if anyone changes the data on the way, the check fails and the browser knows.

The flow, simply: browser asks for a secure connection, server shows its certificate, browser checks it matches the host and is signed by a trusted authority, browser and server agree secret keys for this visit, and all HTTP traffic for the visit travels inside that encrypted channel. The same HTTP rules still apply inside — just in private.

HTTPS protects data on the road. It does not make a web app safe by itself. A site can have perfect HTTPS and still have a broken login, broken access control, or an injection flaw. Those are app problems, and they're the subject of the OWASP file. Don't confuse "the road is sealed" with "the kitchen checks who you are."

On your own machine, `http://127.0.0.1:8000` is safe for learning because the traffic never leaves your computer. That's the exception that proves the rule: localhost stays local. For any real network, even a lab network, prefer HTTPS, and never send a real password over plain HTTP.

## Forms, JSON, APIs

A form is a page asking you to fill in fields and send them. A simple form sends a POST with `Content-Type: application/x-www-form-urlencoded`, meaning the fields become `name=value&name=value` in the body:

```
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 48

username=your-user&password=<test-password>
```

JSON is a plain way to write structured data as text so programs can send it to each other:

```json
{
  "username": "your-user",
  "page": 2,
  "notes": ["first note", "second note"]
}
```

Names in double quotes, values as text, numbers, true or false, null, lists in `[]`, or objects in `{}`. JSON is data, not code — a good server treats it as data and never runs it as code.

An API is a set of URLs and rules programs use to talk to each other instead of humans reading pages, and it usually returns JSON instead of HTML. `GET /api/notes?page=2` might return JSON with a list of notes, while `GET /notes?page=2` returns an HTML page with the same notes for a human. Same data, different shape, different audience.

This is the bridge to the JavaScript APIs you haven't covered yet. `fetch`, promises, and `async/await` are how a page in the browser talks to a web API. Learn the HTTP shape first and the JS code makes sense: `fetch` builds a request, the server answers, your code reads the status and the JSON. The page loads its HTML, its JavaScript runs and calls the API with `fetch`, the server checks the session, does the work, and answers with a status and a JSON body, and the code reads the status first — on 2xx it reads the JSON, on 4xx or 5xx it shows a safe error, not the raw server detail — then the page updates only the part that changed, without reloading.

## How a page loads, end to end

Put it together. You type or click a URL. The browser splits it into scheme, host, port, path, query, fragment. DNS turns the host into an IP, unless it's already `127.0.0.1`. The browser connects on that IP and port, setting up TLS first for HTTPS. It sends the HTTP request — method, path, headers, cookies, body if any. The server checks session and permissions, does the work, and sends back status, headers, body. A redirect sends the browser back to the start with the new location. The browser reads the HTML, then asks for the images, styles, and scripts that page needs — each one another HTTP request through the same steps. JavaScript on the page may call APIs for more data, each one another request. The browser shows the finished page and keeps the cookies for the next visit.

Notice how many separate requests one "page" really is. A slow or broken page is often one slow or broken request among many. Once you learn to read requests one by one, in order, you stop guessing about web problems and start tracing them.

## Lab: a web server on your own machine only

You don't need the internet, and you must not test a real site, to see all of this working.

Make a small lab folder and one page:

```bash
mkdir -p ~/web-lab
cd ~/web-lab
echo "<h1>Hello from my own machine</h1>" > index.html
ls -l
```

Start a simple server that listens on localhost only, port 8000, and leave that terminal open while you test. It serves only the files in `~/web-lab`, only to your own computer:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

In a second terminal, ask for the page with headers included:

```bash
curl -i http://127.0.0.1:8000/
```

Expect a `200 OK` status line, headers like Content-Type and Content-Length, a blank line, then your `<h1>Hello from my own machine</h1>` body. Then ask for something that doesn't exist:

```bash
curl -i http://127.0.0.1:8000/no-such-page
```

Expect `404 Not Found`. Same server, same steps, different status — because the path asked for something the server doesn't have.

Stop the server with `Ctrl+C` when you're done. A lab server left running is a door left open, even on localhost. And two setup errors to avoid: starting the server without `--bind 127.0.0.1`, so it listens on every interface instead of localhost only; and serving a folder that holds real personal files — serve only a small lab folder you made for the lesson, with only lab files in it.

Next file: OWASP Top 10, as a defence map — what each risk means, how it happens in design, and what a safe design does instead. Read it as recognition training for your own lab apps, long before you ever test anyone else's system, and only ever with written permission.

One line to carry forward: the web is a browser asking a server, in HTTP, for a thing named by a URL; the server checks who you are and what you may do, then answers with a status, headers, and a body — and cookies and sessions are how it remembers you between asks.
