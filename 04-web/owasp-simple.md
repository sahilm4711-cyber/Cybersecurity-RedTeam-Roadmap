# 04 — Web: OWASP Top 10, Simply Explained

## What this file is

This is Phase 3, part 2. The previous file, `web-basics.md`, gave you the
map: URLs, HTTP requests and responses, headers, cookies, sessions, and
APIs. This file uses that map to explain the OWASP Top 10 — the ten most
common web application security risks — in simple words.

OWASP (Open Web Application Security Project) is a community that studies
web security and publishes guidance. Its Top 10 is not a list of attacks
to try, and not a checklist to copy. It is a list of design mistakes that
keep appearing in real web apps, ordered by how common and how harmful
they are. Learn it as a defence map: "What mistake is this? How does it
happen? What does a safe design do instead?"

This file uses the OWASP Top 10 (2021) list, because that is the list
most courses, labs, and job descriptions still refer to. If a newer list
appears later, the ideas here still apply — the names may move, but the
mistakes stay the same.

No shortcuts here. Each risk gets: simple meaning, everyday analogy, how
it happens in design, a lab-only defence idea, and a common mistake.

**Safety rule for this whole file — read this first:**
- Practise only on web apps you built yourself, or on a deliberately
  vulnerable lab app installed only on your own machine, for learning.
- Never test any of these risks on a real website, school system, or
  company system. Trying a risk on a real system without written
  permission is unauthorised testing, even if you "only wanted to learn"
  or "did not change anything".
- Never use a real username, real password, real session value, or real
  personal data in any example. Use placeholders such as
  `<your-username>`, `<your-test-password>`, and
  `<example-session-value>`.
- This file explains risks so you can recognise and defend against them.
  It does not give step-by-step attack instructions for real systems, and
  it must not be used that way.

**How to read each risk:**
For each risk, ask four questions, in order:
1. What is the design mistake, in one sentence?
2. What does the server fail to check?
3. What would a safe design check instead?
4. How would I recognise this mistake in my own lab app?

---

## The list at a glance

| Code | Risk, in simple words |
|---|---|
| A01 | Broken Access Control — the server lets you do or see things you should not |
| A02 | Cryptographic Failures — secrets and data are not protected properly |
| A03 | Injection — untrusted input is treated as a command, not as data |
| A04 | Insecure Design — the design itself has no safety rules from the start |
| A05 | Security Misconfiguration — the setup is left open, default, or leaking detail |
| A06 | Vulnerable and Outdated Components — old, known-broken parts are still used |
| A07 | Identification and Authentication Failures — login and identity checks are weak |
| A08 | Software and Data Integrity Failures — updates and data are trusted without checking |
| A09 | Security Logging and Monitoring Failures — attacks happen and nobody notices |
| A10 | Server-Side Request Forgery (SSRF) — the server is tricked into making asks for you |

Keep this table in mind. Now each risk, fully and simply.

---

## A01 — Broken Access Control

**Simple meaning:** The server lets a user see, change, or delete something
they should not be allowed to touch.

**Everyday analogy:** A hotel gives every guest a master key by mistake.
Your key should open only your room. A broken system lets your key open
every room, or lets you walk into the staff office just by knowing the
door number.

**How it happens in design:**
- The page hides a button or link, but the server never checks permission
  when the request actually arrives. Hiding is not checking: anyone who
  knows or guesses the URL can send the request directly.
- The server trusts a value from the request, such as `?user_id=42` in a
  URL, without checking that the logged-in user really is user 42, or is
  allowed to see user 42's data.
- A normal user can change their own request to ask for an admin action,
  and the server does it because it never asked, "Is this user an admin?"

**Lab-only defence idea:**
For every request, on the server, in this order: (1) Who is this, from
the checked session? (2) What are they asking for? (3) Are they allowed
that exact thing? If any answer is no, answer `403 Forbidden` (see
`web-basics.md`, Section 3). Do this check on the server for every
request, not only on pages with a visible button.

**Common mistake:** Thinking "nobody will guess this URL" is a defence.
URLs are guessed, shared, logged, and found. A secret URL is not access
control; a server-side permission check is.

---

## A02 — Cryptographic Failures

**Simple meaning:** Secrets and sensitive data are not protected properly
while they are stored, or while they travel.

**Everyday analogy:** Writing a secret on a postcard instead of sealing it
in an envelope, or locking a safe but leaving the key taped to the safe.

**How it happens in design:**
- Sensitive data travels over plain HTTP instead of HTTPS, so anyone on
  the road can read it (see `web-basics.md`, Section 6).
- Passwords are stored in plain text, or with a weak, fast, reversible
  method, instead of a proper, slow password-hashing method designed for
  passwords. If the store is ever read, every password is readable.
- Old or broken encryption is used, keys are hard-coded in the code, or
  the same key is used everywhere and never changed.

**Lab-only defence idea:**
Use HTTPS for any real network traffic. Store passwords only as hashes
made by a password-hashing method, never in plain text, and never in a
lab file you might share. Keep secrets out of code and out of public
files: use placeholders such as `<your-test-password>` in examples, and
a private, local store for real lab values, never a shared repo.

**Common mistake:** Thinking "we use encryption" is enough. What matters
is: which method, which key, where the key lives, and whether the data
is protected both in travel and in storage. A weak method, or a leaked
key, breaks the whole promise.

---

## A03 — Injection

**Simple meaning:** The app takes input from a user and treats it as a
command or a query, instead of treating it as plain data.

**Everyday analogy:** You write a note that says "call me", and the
reader treats the note itself as an order to do something else, because
nobody separated "the message" from "the instruction".

**How it happens in design:**
- A search box, login form, or URL value is joined directly into a
  database query, a system command, or a page, without separating data
  from code. The input then changes what the query or command means.
- The classic form is SQL injection: input meant as a name or search word
  changes the database query itself. The same idea applies to other
  places where input becomes code: commands, page scripts, and template
  text.

**Lab-only defence idea — the algorithm:**
1. Treat every input from a request as untrusted data, never as code.
2. Use parameterised queries (also called prepared statements) for the
   database: the query shape is fixed first, and the input is sent
   separately, only as a value. The input can then never change the
   shape of the query.
3. Check input against what is expected: type, length, and allowed
   characters, for that field — and still use step 2, because checking
   alone is not enough.
4. When showing data back in a page, encode it so the browser treats it
   as text, not as page code.

**Common mistake:** Trying to "block bad words" from input, such as
blocking one word or symbol. Attackers in a lab simply use another
spelling. Fix the design — separate data from code — instead of playing
word games with input.

---

## A04 — Insecure Design

**Simple meaning:** The app was designed without safety rules from the
start, so no single fix later can make it safe. This is not one bug; it
is a missing design.

**Everyday analogy:** Building a bank with no vault, then trying to make
it safe by adding a stronger front door. The design itself never asked,
"What must never be allowed, even if someone tries hard?"

**How it happens in design:**
- Nobody wrote down the threats before building: who may do what, what
  must never leak, what happens after too many failed logins, what
  happens when a session ends.
- There is no limit on tries, no check on who may do what, and no plan
  for what happens when something fails. The app "works" in normal use
  and breaks under misuse, because misuse was never designed for.
- Safety is added at the end, as an afterthought, instead of being part
  of each feature's design: what are the inputs, the trusted parts, and
  the failure points? (That three-part question is a habit worth keeping
  for every feature you build.)

**Lab-only defence idea:**
Before you build a lab feature, write three lines: (1) Inputs: what can
arrive here, from whom? (2) Trusted parts: what must the server check
before it acts? (3) Failure points: what happens if the input is wrong,
missing, or hostile? Then build the checks first, and only then the
feature. A small design note like this, written before code, prevents a
whole class of later fixes.

**Common mistake:** Treating design as "extra work for later". Later
never comes, or it comes as a rewrite. A ten-minute design note at the
start is cheaper than a security fix at the end.

---

## A05 — Security Misconfiguration

**Simple meaning:** The app or server is set up in an unsafe way: defaults
left in place, too much detail shown, or doors left open that should be
closed.

**Everyday analogy:** Moving into a new house and leaving the builder's
default lock code, the spare key under the mat, and a sign listing every
room and what is inside it.

**How it happens in design:**
- Default usernames, passwords, or settings are left unchanged in a lab
  or real system. Defaults are public knowledge; leaving them is leaving
  the door open.
- Error pages show full detail: database errors, file paths, software
  versions, or stack traces. That detail teaches an attacker how the
  system is built.
- Unneeded features, sample pages, and test accounts are left enabled.
  Every extra feature is an extra door, and a test account is a door
  with a known key.
- Security headers are missing, so the browser is not told the safe
  rules for this site (for example, how cookies should be handled, or
  which content may run as code).

**Lab-only defence idea:**
Make a small setup checklist for your own lab server, and run it every
time: change all defaults; remove sample pages and test accounts you do
not need; show users only a short, safe error message, and keep full
detail in a private log on your own machine; close doors you do not use;
and keep a note of what you changed, so you can check it again later.

**Common mistake:** Thinking misconfiguration is "not a real bug"
because the code itself works. In practice, an open default or a leaking
error page is often the first door an attacker in a lab walks through —
before any clever flaw is needed.

---

## A06 — Vulnerable and Outdated Components

**Simple meaning:** The app uses old libraries, frameworks, or parts that
already have known, public flaws, and nobody updated them.

**Everyday analogy:** Building a house with locks that everyone already
knows how to open, because the lock design was broken years ago and a
fixed version exists, but nobody fitted it.

**How it happens in design:**
- A project uses a library once and never checks it again. The library
  later gets a known flaw, a fix is published, but the app still runs
  the old version.
- Nobody keeps a list of which components, and which versions, the app
  uses. If you do not know what you use, you cannot know what needs
  updating.
- Updates are feared because "it works now", so the app falls further
  behind, and updating later becomes harder and riskier.

**Lab-only defence idea:**
Keep a simple list for each lab project: component name, version, and
where you got it. Check that list from time to time against the
component's own update notes, and update in your own lab first, then test
that the lab app still works. Update one component at a time, so if the
lab breaks, you know which change broke it.

**Common mistake:** Updating everything at once, in a hurry, with no
test. Or the opposite: never updating at all. Both fail. Small, checked,
one-at-a-time updates, in your own lab, are the habit that works.

---

## A07 — Identification and Authentication Failures

**Simple meaning:** The app does not reliably confirm who someone is, or
it lets an attacker pretend to be someone else, or keep a session they
should have lost.

**Everyday analogy:** A club that checks IDs badly: it accepts a photo
of an ID, lets you in with someone else's ticket, or never takes the
ticket back when you leave.

**How it happens in design:**
- Weak or missing rules: short passwords allowed, no limit on login
  tries, or the same session kept after login instead of a fresh one.
- Session IDs that are short, guessable, or never expire, or a logout
  that only hides the page but leaves the server session alive (see
  `web-basics.md`, Section 5).
- Error messages that tell an attacker which part was wrong: "username
  does not exist" vs "wrong password" lets someone work out which
  usernames exist, one try at a time.
- Password reset or "forgot password" flows that leak who has an account,
  or that accept a weak, guessable reset value.

**Lab-only defence idea — the algorithm:**
1. After a successful login, give a fresh, long, random session ID, and
   drop the old one.
2. Limit login tries, and slow down repeated failures, so guessing
   becomes slow and visible.
3. Use the same short, safe error for a failed login, whatever failed:
   "Username or password is incorrect." Do not say which part was wrong.
4. Make sessions expire, and make logout actually delete the session on
   the server, not just hide the page.
5. Store passwords only as proper password hashes (A02), never in plain
   text, and never in a shared file.

**Common mistake:** Making login "easy" by removing checks. Every check
you remove for convenience is a check an attacker in a lab will also
enjoy. Convenience and safety must be designed together, not traded
blindly.

---

## A08 — Software and Data Integrity Failures

**Simple meaning:** The app trusts updates, plugins, or data without
checking that they really came from the right source and were not
changed on the way.

**Everyday analogy:** Accepting a delivery box without checking the
sender, the seal, or whether the contents were swapped — then installing
whatever is inside, on trust alone.

**How it happens in design:**
- Updates, libraries, or plugins are downloaded and run without checking
  a signature, hash, or trusted source. If the download was changed on
  the way, the app runs the changed version.
- Data that should be trusted — such as a signed value, a stored
  setting, or an uploaded file — is accepted without checking who made
  it, or whether it was changed after it was made.
- A build or deployment step runs code from an untrusted place, so one
  changed part can change the whole app.

**Lab-only defence idea:**
Get lab software only from its official source, check its signature or
hash when the project gives one, and keep a note of what you installed
and where it came from. Treat any file, update, or value that arrives
from outside your own lab as untrusted until you have checked its
source — the same rule as input in A03, applied to software and data.

**Common mistake:** Thinking "it came from the internet, so it must be
fine". The internet is a road, not a guarantee. Source and signature are
the guarantee, and both must be checked.

---

## A09 — Security Logging and Monitoring Failures

**Simple meaning:** When something goes wrong, or someone tries to break
in, the app does not record it, or nobody ever looks at the record — so
the attack continues, unnoticed, for a long time.

**Everyday analogy:** A shop with no cameras and no alarm log. A thief
can try the door every night, and nobody knows, because nobody recorded
the tries, and nobody checked.

**How it happens in design:**
- Failed logins, permission denials (`403`), strange inputs, and errors
  are not logged at all, or are logged only on the user's screen.
- Logs exist but are never read, or they are so noisy that a real attack
  hides among thousands of harmless lines.
- Logs store secrets themselves — passwords, session values, or personal
  data — so the log becomes a leak of its own.

**Lab-only defence idea:**
Log the important events on the server, in your own lab, without secrets:
time, what was asked for, who the session claimed to be, the result
(success, `401`, `403`, `404`), and the reason in general terms. Then,
once in a while, actually read the log for your own lab app and ask:
"Does anything here look like repeated tries, or asks for things that
should not exist?" A log nobody reads is decoration, not defence.

**Common mistake:** Logging everything, including secrets, and reading
nothing. Good logging is small, safe, and actually reviewed. In a lab,
start with logins, permission denials, and errors — and keep secrets
out of every log line.

---

## A10 — Server-Side Request Forgery (SSRF)

**Simple meaning:** The server is tricked into making a request, on your
behalf, to a place you should not be able to reach — often an internal
service that only the server itself should talk to.

**Everyday analogy:** You ask a hotel receptionist to "fetch something
from the back office for me". The receptionist can enter the back office,
but you cannot. If the receptionist never checks what you asked for, you
just used their access as your own.

**How it happens in design:**
- The app takes a URL, or a server name, from a user request, and the
  server itself fetches that URL — for example, to fetch an image, check
  a link, or call another service — without checking where that URL
  really points.
- The supplied URL points to an internal address, such as a localhost
  service or a private network address, that the user could never reach
  directly, but the server can. The server's answer then leaks what that
  internal service said, or lets the user trigger an internal action.

**Lab-only defence idea — the algorithm:**
1. Never let a user-supplied URL decide where the server fetches from,
   without a strict check.
2. Allow only a small, written list of hosts and schemes that the
   feature truly needs, and reject everything else.
3. Resolve the host name yourself, and reject the request if it resolves
   to a private, localhost, or link-local address, unless that exact
   internal call is a deliberate, designed part of the feature.
4. Never return the raw internal response to the user. Return only the
   safe, checked result the feature needs.

**Common mistake:** Checking only the text of the URL, not where it
really points. A name can resolve to an internal address, and a redirect
can move a request from a safe place to an internal one. Check the
resolved destination, not just the spelling of the URL.

---

## How the ten fit together — a study order

Do not try to memorise all ten at once. Learn them in this order, because
each one builds on the last:

1. **Start with the map:** A01, A07, and A03. These are the core of web
   work: who may do what, who are you really, and is input treated as
   data or as code?
2. **Then the setup:** A05 and A06. A working app with open defaults and
   old parts is unsafe before any clever flaw is found.
3. **Then the design:** A04, A02, and A08. These ask whether the design
   itself, the protection of secrets, and the trust in updates and data
   were thought through.
4. **Then the watching:** A09 and A10. These ask whether anyone would
   notice an attack, and whether the server itself can be turned into a
   tool for the attacker.

**A lab study habit for each risk:**
Pick one risk per study session. Write, in simple English, without
looking back: (1) the mistake in one sentence, (2) what the server
fails to check, (3) what a safe design checks instead, (4) one way you
would recognise it in your own lab app. If you cannot write all four,
re-read that section only, then write again. That short English
explanation is also your communication practice — the clear,
step-by-step style you are building.

---

## Safe practice, and what comes after this file

Safe practice for this whole phase, in one paragraph: build a tiny lab
web app of your own, on your own machine, with a login, a session, and
one or two pages; then, deliberately and only in that lab, make one
design mistake at a time, see what breaks, and fix it with the defence
idea in this file. One mistake, one fix, one note. That is how the Top 10
becomes real skill instead of a list you once read.

What comes after: later phases will return to the web with tools and
with lab testing, step by step, and only ever on your own lab systems or
systems you have written permission to test. This file is the map for
that later work, not permission to start it on real systems. The next
roadmap commits will cover tools, practice labs, and resources, in the
same big, detailed, simple style — each one only after your approval,
and each one checked for generic placeholders only, with no personal
usernames, hostnames, or passwords.

**One-line summary to remember:**
Most web risk is not magic: it is a server that failed to check who you
are, what you may do, or whether input is data or code — and a safe
design checks all three, on the server, for every request.
