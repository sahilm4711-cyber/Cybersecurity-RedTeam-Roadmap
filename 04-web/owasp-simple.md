# Web: OWASP Top 10 as a Defence Map

The web-basics file gave you the map: URLs, HTTP, headers, cookies, sessions, APIs. This file uses that map for the OWASP Top 10 — the ten most common web application security risks, in plain words.

OWASP is a community that studies web security and publishes guidance. Its Top 10 is not a list of attacks to try, and not a list to copy. It's a list of design mistakes that keep showing up in real web apps, ordered by how common and harmful they are. Learn it as a defence map: what mistake is this, how does it happen, what does a safe design do instead?

This uses the 2021 list, because that's the one most courses, labs, and job descriptions still refer to. If a newer list appears later, the ideas still apply — names may move, the mistakes stay the same.

Safety rule for the whole file: practise only on apps you built yourself, or a deliberately vulnerable lab app on your own machine. Never test these risks on a real website, school, or company system — without written permission that's unauthorised testing, even if you "only wanted to learn." Never use a real username, password, or session value — placeholders like `your-user`, `<test-password>`, and `<example-session-value>` only. This file explains risks so you can defend against them, with no step-by-step attack instructions for real systems.

For each risk, ask: what is the mistake, in one sentence? What does the server fail to check? What would a safe design check instead? How would I recognise it in my own lab app?

## The list at a glance

| Code | Risk, in plain words |
|---|---|
| A01 | Broken Access Control — the server lets you do or see things you shouldn't |
| A02 | Cryptographic Failures — secrets and data aren't protected properly |
| A03 | Injection — untrusted input is treated as a command, not as data |
| A04 | Insecure Design — the design itself has no safety rules from the start |
| A05 | Security Misconfiguration — the setup is left open, default, or leaking detail |
| A06 | Vulnerable and Outdated Components — old, known-broken parts are still used |
| A07 | Identification and Authentication Failures — login and identity checks are weak |
| A08 | Software and Data Integrity Failures — updates and data are trusted without checking |
| A09 | Security Logging and Monitoring Failures — attacks happen and nobody notices |
| A10 | Server-Side Request Forgery (SSRF) — the server is tricked into making asks for you |

## A01 — Broken Access Control

The server lets a user see, change, or delete something they shouldn't be allowed to touch.

Picture a hotel giving every guest a master key by mistake. Your key should open only your room. A broken system lets it open every room, or lets you walk into the staff office just by knowing the door number.

It usually happens two ways. The page hides a button, but the server never checks permission when the request actually arrives — hiding is not checking, and anyone who knows or guesses the URL can send the request directly. Or the server trusts a request value like `?user_id=42` without checking the logged-in user really is user 42, or may see user 42's data.

The defence is a server-side check for every request: who is this, from the checked session? What are they asking for? Are they allowed that exact thing? If any answer is no, answer `403 Forbidden`. "Nobody will guess this URL" is not a defence — URLs are guessed, shared, logged, and found.

## A02 — Cryptographic Failures

Secrets and sensitive data aren't protected properly while they're stored, or while they travel.

It's a secret on a postcard instead of sealed in an envelope, or a safe with the key taped to it. Sensitive data travels over plain HTTP, so anyone on the road can read it. Passwords are stored in plain text, or with a weak, fast, reversible method, instead of a proper slow password hash — so if the store is ever read, every password is readable. Or old, broken encryption is used, or keys are hard-coded or never changed.

The defence is straightforward to state, harder to keep disciplined about: HTTPS for any real network traffic; passwords stored only as hashes from a password-hashing method, never in plain text, never in a file you might share; secrets kept out of code and out of public files, placeholders in examples, real lab values in a private local store only.

"We use encryption" is not enough on its own. What matters is which method, which key, where the key lives, and whether data is protected both in travel and in storage. A weak method or a leaked key breaks the whole promise.

## A03 — Injection

The app takes input from a user and treats it as a command or a query, instead of treating it as plain data.

A search box, login form, or URL value gets joined directly into a database query, a system command, or a page, without separating data from code — and the input changes what the query or command means. The classic form is SQL injection, where input meant as a search word changes the database query itself. The same idea applies wherever input becomes code: commands, page scripts, template text.

The defence needs both layers. Treat every input as untrusted data, never as code. Use parameterised queries — prepared statements — so the query shape is fixed first and input is sent separately, only as a value, and can never change the query's shape. Check input against what's expected — type, length, allowed characters — and still use parameterised queries, because checking alone isn't enough. When showing data back in a page, encode it so the browser treats it as text, not page code. "Blocking bad words" is the wrong fix: block one word and a lab attacker uses another spelling.

## A04 — Insecure Design

The app was designed without safety rules from the start, so no single fix later can make it safe. This isn't one bug; it's a missing design.

It's a bank built with no vault, then made "safe" by adding a stronger front door. The design never asked: what must never be allowed, even if someone tries hard? Nobody wrote down the threats before building — who may do what, what must never leak, what happens after too many failed logins, what happens when a session ends. There's no limit on tries, no check on who may do what, no plan for failure. The app works in normal use and breaks under misuse, because misuse was never designed for. Safety gets added at the end, as an afterthought.

The habit that prevents most of this is cheap: before you build a lab feature, write three lines — inputs (what can arrive, from whom?), trusted parts (what must the server check before it acts?), failure points (what happens if input is wrong, missing, or hostile?). Build the checks first, then the feature.

## A05 — Security Misconfiguration

The app or server is set up in an unsafe way: defaults left in place, too much detail shown, doors left open that should be closed.

Moving into a new house and leaving the builder's default lock code, the spare key under the mat, and a sign listing every room and what's inside it. Default usernames, passwords, or settings are left unchanged — and defaults are public knowledge, so leaving them is leaving the door open. Error pages show full detail: database errors, file paths, software versions, stack traces — detail that teaches an attacker how the system is built. Unneeded features, sample pages, and test accounts are left enabled. Every extra feature is an extra door, and a test account is a door with a known key. Security headers are missing, so the browser is never told the safe rules for the site.

For your own lab server, run a small setup note every time: change all defaults; remove sample pages and test accounts you don't need; show users only a short, safe error message, full detail in a private log; close doors you don't use. Misconfiguration gets dismissed as "not a real bug" because the code works — in practice an open default or leaking error page is often the first door someone walks through.

## A06 — Vulnerable and Outdated Components

The app uses old libraries, frameworks, or parts that already have known, public flaws, and nobody updated them.

A house built with locks everyone already knows how to open, because the lock design broke years ago and a fixed version exists, but nobody fitted it. A project uses a library once and never checks it again. The library later gets a known flaw, a fix is published, but the app still runs the old version. Nobody keeps a list of which components, and which versions, the app uses — and if you don't know what you use, you can't know what needs updating. Updates get feared because "it works now," so the app falls further behind, and updating later becomes harder and riskier.

Keep a list for each lab project: component name, version, where you got it. Check it against the component's update notes, update in your own lab first, test that the app still works, and update one component at a time so a break points at one change.

## A07 — Identification and Authentication Failures

The app doesn't reliably confirm who someone is, or it lets someone pretend to be someone else, or keep a session they should have lost.

A club that checks IDs badly: it accepts a photo of an ID, lets you in with someone else's ticket, or never takes the ticket back when you leave. The usual shapes: weak rules — short passwords allowed, no limit on login tries, the same session kept after login instead of a fresh one. Session IDs that are short, guessable, or never expire, or a logout that only hides the page but leaves the server session alive. Error messages that say which part was wrong — "username does not exist" versus "wrong password" lets someone work out which usernames exist, one try at a time. Password reset flows that leak who has an account, or accept a weak, guessable reset value.

The defence: after login, give a fresh, long, random session ID and drop the old one. Limit login tries and slow down repeated failures. Use the same safe error whatever failed — "Username or password is incorrect" — and don't say which part was wrong. Make sessions expire, and make logout actually delete the session on the server. Store passwords only as proper hashes, never in plain text.

## A08 — Software and Data Integrity Failures

The app trusts updates, plugins, or data without checking they really came from the right source and weren't changed on the way.

Accepting a delivery box without checking the sender, the seal, or whether the contents were swapped — then installing whatever is inside, on trust alone. Updates, libraries, or plugins get downloaded and run without checking a signature, hash, or trusted source, so if the download was changed on the way, the app runs the changed version. Data that should be trusted — a signed value, a stored setting, an uploaded file — gets accepted without checking who made it or whether it was changed after it was made. A build or deployment step runs code from an untrusted place, so one changed part can change the whole app.

Get lab software only from its official source, check its signature or hash when the project gives one, and keep a note of what you installed and where it came from. Treat anything arriving from outside your own lab as untrusted until you've checked its source — the same rule as input in A03, applied to software and data.

## A09 — Security Logging and Monitoring Failures

When something goes wrong, or someone tries to break in, the app doesn't record it, or nobody ever looks at the record — so the attack continues, unnoticed, for a long time.

A shop with no cameras and no alarm log. Someone can try the door every night and nobody knows, because nobody recorded the tries and nobody checked. Failed logins, permission denials (403), strange inputs, and errors aren't logged at all, or are logged only on the user's screen. Logs exist but are never read, or they're so noisy a real attack hides among thousands of harmless lines. Or logs store secrets themselves — passwords, session values, personal data — so the log becomes a leak of its own.

Log the important events on the server, in your own lab, without secrets: time, what was asked for, who the session claimed to be, the result (success, 401, 403, 404). Then actually read the log once in a while and ask whether anything looks like repeated tries. A log nobody reads is decoration, not defence — start with logins, permission denials, and errors, and keep secrets out of every line.

## A10 — Server-Side Request Forgery (SSRF)

The server is tricked into making a request, on your behalf, to a place you shouldn't be able to reach — often an internal service that only the server itself should talk to.

You ask a hotel receptionist to "fetch something from the back office for me." The receptionist can enter the back office, but you can't. If the receptionist never checks what you asked for, you've just used their access as your own. It happens when the app takes a URL from a user request and the server itself fetches it — to fetch an image, check a link, call another service — without checking where it really points. That URL may point to an internal address, like a localhost service, that the user could never reach directly but the server can, and the server's answer then leaks what that internal service said.

The defence: never let a user-supplied URL decide where the server fetches from without a strict check. Allow only a small, written list of hosts and schemes the feature truly needs. Resolve the host yourself, and reject it if it resolves to a private, localhost, or link-local address, unless that internal call is a deliberate part of the feature. Never return the raw internal response — only the safe, checked result.

The subtle failure here is checking only the text of the URL, not where it really points. A name can resolve to an internal address, and a redirect can move a request from a safe place to an internal one. Check the resolved destination, not just the spelling of the URL.

## How the ten fit together

Don't try to memorise all ten at once. Learn them in an order where each builds on the last.

Start with the core of web work: A01, A07, and A03 — who may do what, who are you really, and is input treated as data or as code? Then the setup: A05 and A06 — a working app with open defaults and old parts is unsafe before any clever flaw is found. Then the design: A04, A02, and A08 — whether the design itself, the protection of secrets, and trust in updates and data were thought through. Then the watching: A09 and A10 — whether anyone would notice an attack, and whether the server itself can be turned into a tool for the attacker.

Safe practice for this phase: build a tiny lab web app of your own, with a login, a session, and one or two pages; then, only in that lab, make one design mistake at a time, see what breaks, and fix it with the defence idea in this file. One mistake, one fix, one note. That's how the Top 10 becomes skill instead of a list you once read. Later phases return to the web with tools and lab testing, only ever on your own lab systems or systems you have written permission to test.

One line to carry forward: most web risk is not magic — it's a server that failed to check who you are, what you may do, or whether input is data or code, and a safe design checks all three, on the server, for every request.
