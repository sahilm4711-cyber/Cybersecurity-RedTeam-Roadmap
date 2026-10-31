# 05 — Tools: Web Tools, Fully Explained

## What this file is

This is Phase 4, part 3. You already know web basics, OWASP risks, and the tool mindset. Now you learn web tools — the tools you use to understand, check, and test a lab website you built yourself.

Web tools do not make you a tester by themselves. They make visible what is already there: requests, responses, headers, cookies, and design flaws in your own lab app. The skill is in reading those things, tracing them, and writing a clear note about them.

No shortcuts here. Each tool class gets: simple explanation, what, why, when, when NOT to use it, lab example on your own machine only, output meaning, and common mistakes.

**Safety rule for this whole file:**
Web tools here are for a lab website you built yourself, or a deliberately vulnerable lab app installed only on your own machine, only.
- Never use a web proxy, scanner, or tester against a real website, login, or API without written permission.
- Use test data only: test usernames like `test-user`, test passwords as placeholders like `<test-password>`, and sample pages you created yourself.
- Never put a real username, password, token, cookie value, or real URL with a secret in a note, screenshot, or public file. Use placeholders: `<lab-url>`, `<test-password>`, `<session-id>`, `<token>`.
- A web tool result is not permission to exploit a finding. In lab, a finding means: write a note, fix the design in your own lab app, and re-test your own fix.

---

## 1. Browser developer tools — your first web tool, already installed

**What:**
Developer tools are built into your browser. They show you, for a page you open: network requests, HTML structure, CSS, JavaScript, cookies, storage, and console messages.

**Why:**
Before you use any other web tool, learn developer tools. They show you the same requests and responses that every other web tool shows, but with no extra setup, and with no risk, when you use them on your own lab page.

**When:**
Every time you open a lab page, to trace what it actually does. Especially when a lab page does not work as expected, or when you want to understand a lab login, a lab form, or a lab API call.

**When NOT to use it:**
Do not use developer tools to inspect, copy, or test a real website's private data, sessions, or APIs without permission. Opening developer tools on a real site to "see how it works" and then testing what you find is still testing without permission.

**Lab example — own lab page only:**
1. Open your own lab page in a browser, at `http://127.0.0.1:<lab-port>/`.
2. Open developer tools, Network tab, and reload the page once.
3. Click the main HTML request. Read: method, status code, request headers, response headers, and body, in your own words.
4. Click a CSS or JavaScript request. Note why the browser needed it.
5. Open the Application / Storage tab. Find cookies for your lab site only. Note the cookie names (not values), and whether HttpOnly is present.
6. Close developer tools when you are done. Do not copy cookie values or tokens into a note.

**Output meaning:**
- Network tab, one row = one HTTP request, with method, path, status, size, and time.
- Status 200 = that request succeeded, for that resource, at that time.
- A request with a large size or long time is a clue, not a verdict. It may be a large image, a slow lab service, or a missing cache — check the resource itself, in lab.
- Console errors are clues about JavaScript or loading problems in your lab page, not proof of a security flaw.

**Common mistakes:**
- Copying a full network log with real tokens or cookies into a screenshot or report. Summarise with placeholders.
- Thinking developer tools can "hack" a page. They only show what the browser already received. Changing something in developer tools only changes your own browser view, not the server.
- Forgetting to clear the Network tab before reloading, and then reading old requests as if they were new.

**Practice task:**
Open your own lab page, reload once with the Network tab open, and write a 5-line trace: main request, method, status, one important header, and one cookie name (not value).

---

## 2. `curl` as a web tool — the browser without the page

**What:**
`curl` is a command-line tool that makes HTTP requests and shows the response, without rendering a page. You met it in network tools. Here, it is a web tool, because it lets you see the raw request and response, and test one lab endpoint at a time.

**Why:**
A browser hides details for good user experience. `curl` shows them: status line, headers, body, and, with the right flags, the request it sent. That is how you learn HTTP, and how you test a lab endpoint without a browser's help.

**When:**
Against your own lab web service only, one endpoint at a time, with test data only.

**When NOT to use it:**
Do not use `curl` to test a real login, API, or form, do not send crafted requests to a real site, and do not download and run a script from the internet without reading it first, in lab or elsewhere.

**Lab example — own lab service only:**

```bash
curl -i http://127.0.0.1:<lab-port>/
curl -i http://127.0.0.1:<lab-port>/lab-notes
```

- `-i` = include response headers in the output, not just the body.
- The first command asks for the lab home page. The second asks for a lab notes page you created yourself, if you have one.

**Output meaning, line by line, lab only:**
- `HTTP/1.1 200 OK` = that lab endpoint returned success for that request.
- `Content-Type: text/html` = the body is HTML. `application/json` = the body is JSON.
- `Set-Cookie: session_id=<...>` = the lab server asked the browser to store a session cookie. In a note, write `<session-id>`, not the real value.
- Body = the actual page or data. Read it as data, not as code, unless it is a page you wrote yourself in lab.

**Common mistakes:**
- Sending a real password or token with `curl` and then sharing the command history or screenshot publicly. Use test data and placeholders in notes.
- Thinking a `curl` success means a service is secure. It only means it responded to that one request.
- Using `curl` to follow redirects blindly, or to download a file, without checking what the file is first, in lab.

---

## 3. Web proxies in lab — seeing requests in the middle, on your own traffic only

**What:**
A web proxy, in lab, sits between your browser and your lab server. Your browser sends requests to the proxy, the proxy forwards them to the lab server, and it shows you each request and response, so you can read, pause, or change them — for your own lab traffic only.

**Why:**
A proxy makes the invisible visible. A browser hides some steps. A proxy shows every request, in order, with headers and body, so you can trace a lab login, a lab form, or a lab API call, step by step.

**When:**
In your own private lab only, with your own browser, your own lab server, and test data only. Set the proxy in your browser for lab traffic only, and turn it off when you are done.

**When NOT to use it:**
Never set a proxy to intercept traffic from other people, never intercept traffic on a shared, school, or company network, and never use a proxy to change requests to a real website. Intercepting other people's traffic is a privacy violation, not learning.

**Lab concept — no proxy command to run on a real site:**
1. Start your own lab web service, with test data only, on localhost.
2. Start a lab proxy tool on your own machine, following its own documentation, for lab traffic only.
3. Configure your own browser to use that proxy for lab traffic only, if the tool requires it.
4. Open your own lab page once, and read the proxy log: one request, method, path, status, headers, body, in order.
5. If your lab proxy allows it, pause one lab request, read it, and release it unchanged, to see how pausing works. Do not change a request to bypass a lab check, unless you are deliberately testing your own lab fix, with your own data, and you write a note about what you changed and why.
6. Turn the proxy off, restore your browser settings, and stop the lab service when you are done.

**Output meaning:**
- A proxy log is a list of HTTP requests and responses, in order, for your own lab traffic. Each row is one request, like the Network tab, but with more detail about headers and body.
- A request you changed in a proxy is not what the browser originally sent. Note, in your lab notes, whether a request was original or changed, and what you changed, with test data only.
- A proxy does not prove a flaw. It proves what requests were made, and what responses came back, in that lab session.

**Common mistakes:**
- Leaving a proxy on after lab, so all your browsing goes through it. Turn it off when lab ends.
- Intercepting traffic that is not yours. That is the line between lab learning and privacy violation.
- Sharing a proxy log with real tokens, cookies, or personal data. Summarise with placeholders, always.

---

## 4. Web scanners in lab — automated checks, with limits, on your own lab app only

**What:**
A web scanner is a tool that automatically sends many requests to a web app, looking for common design flaws: missing headers, known patterns, error messages, and so on. It is a checklist, run by a program, not a human tester.

**Why:**
A scanner can find obvious, common issues in your own lab app quickly, so you can fix them and learn what they look like. It cannot understand your lab app's logic, its permissions, or its business rules — only a human, tracing requests, can do that.

**When:**
Only against a lab web app you built yourself, or a deliberately vulnerable lab app installed only on your own machine, after you have written down why you are scanning it, and only with a small, slow scan, one lab app at a time.

**When NOT to use it:**
Never run a web scanner against a real website, login, or API. Scanners send many requests, they are noisy, they are logged, and they can cause damage (for example, by creating, changing, or deleting data through forms). Running a scanner without written permission is unauthorised testing, even if you "only wanted to check".

**Lab concept — no scanner command to run on a real site:**
1. Build or install a lab web app on your own machine, with test data only.
2. Write down, in one sentence, what you want the scanner to check in lab.
3. Run the scanner once, slowly, against that one lab app only, with its default safe settings for lab, if it has them.
4. Read each finding, one by one. For each finding, ask: Is this a real design flaw in my lab app, or a false positive? What request caused it? What would a safe design do instead?
5. Fix one finding in your own lab app, re-run the scanner once for that finding only, and note whether it is gone.
6. Stop the lab app and the scanner when you are done. Do not publish the scanner report with real data — write a summary with placeholders.

**Output meaning:**
- A scanner finding is a clue, not a verdict. It says: "This lab app behaved in a way that matches a common pattern." It does not say: "This app is exploitable" or "This app is safe".
- False positives are normal. A scanner does not know your lab app's intent. You must check each finding by tracing the request and response yourself, in lab.
- No findings does not mean secure. A scanner only checks patterns it knows, with inputs it tries. It does not check your app's logic, permissions, or custom flaws.

**Common mistakes:**
- Treating a scanner report as a hacking report, and copying it into a resume or public file without understanding each finding.
- Running a scanner with aggressive settings, in lab or elsewhere, without understanding what those settings do. Aggressive settings can change or delete data, even in lab.
- Scanning a real site "because the scanner is free" or "because everyone does it". Free and common are not permission.

---

## 5. Putting web tools together: a lab web check, step by step

**Algorithm: WEB_CHECK_MY_LAB_APP**

```
Step 1: Question: What lab web app am I checking, and why?
Step 2: Start the lab app on localhost, with test data only.
Step 3: Open the lab app in a browser, with developer tools Network tab open. Reload once. Trace the main request.
Step 4: Run curl -i once against the same lab URL. Compare the status and headers with the browser trace. Do they match?
Step 5: If you have a lab proxy, read the proxy log for that one lab session, in order. Note any request you do not understand.
Step 6: If you have a lab scanner, run it once, slowly, against that one lab app only. Read each finding, one by one, and check it by tracing the request yourself.
Step 7: For each real flaw you find in your own lab app, write a note: what it is, what request caused it, what a safe design does instead, and how you fixed it in lab.
Step 8: Re-test your fix once, with the same tool, for that finding only.
Step 9: Clean up: stop the lab app, turn off the proxy, close developer tools, do not publish real tokens, cookies, or data.
```

**Design note:**
This algorithm is the web version of the tool mindset from `tools-overview.md`: question first, smallest tool first, trace first, note first, fix in lab, re-test once. A professional web tester follows this pattern, with written permission, on a real scope, later. A learner follows it now, in lab, to build the habit.

---

## 6. Common mistakes across web tools

- Using a web tool on a real site, because a browser or a tool makes it easy. Ease is not permission.
- Sharing a full request, response, proxy log, or scanner report with real tokens, cookies, passwords, or personal data. Summarise with placeholders.
- Trusting a scanner finding without tracing the request yourself, in lab.
- Leaving a proxy on, a lab server running, or a lab app exposed after lab ends. Close everything you opened.
- Thinking a web tool result is a verdict on a whole site. It is evidence for one request, one endpoint, one lab session, at one time.
- Forgetting that a web tool can change data, even in lab, if it submits a form or follows a link that deletes something. In lab, use test data, and be ready to rebuild your lab app if a test changes it.

---

## 7. Practice tasks — lab only

1. Developer tools: Open your own lab page, reload once with Network tab open, and write a 5-line trace with placeholders only.
2. `curl`: Run `curl -i` once against your own lab service, and write the status, Content-Type, and one important header, in your own words.
3. Proxy, concept: Draw, on paper, the path browser -> proxy -> lab server -> proxy -> browser, and label what the proxy sees at each step, for your own lab traffic only.
4. Scanner, concept: Write, in one sentence, what a web scanner can find in your own lab app, and in one sentence, what it cannot find.
5. Full web check: Run the WEB_CHECK_MY_LAB_APP algorithm once, slowly, on a lab app you built yourself, with test data only, and write a short summary with placeholders only.

---

## 8. Checklist — you are ready to move on when you can do all of these

- [ ] I can use browser developer tools on my own lab page, and trace one request: method, status, headers, body.
- [ ] I can use `curl -i` on my own lab service, and explain status, Content-Type, and Set-Cookie, without copying secrets.
- [ ] I can explain, simply, what a web proxy does, for my own lab traffic only, and why it must be turned off after lab.
- [ ] I can explain, simply, what a web scanner does, what it can find, and what it cannot find, in my own lab app only.
- [ ] I can run the WEB_CHECK_MY_LAB_APP algorithm once, slowly, with clear notes using placeholders only.
- [ ] I can explain, for any web tool finding, whether it is a real flaw in my lab app, a false positive, or still unknown, by tracing the request myself.
- [ ] I have not used a web proxy, scanner, or tester against a real website, login, or API without written permission.
- [ ] My notes contain no real username, password, token, cookie value, or real URL with a secret — only generic placeholders.

**Next phase:**
After tools, the roadmap moves to password and system tools, safe labs, practice notes, books, courses, and checklists — in the same big, detailed, simple style, one approved commit at a time, lab only, with generic placeholders only.

---
*All examples in this file are for your own machine or your own private lab only. Use test data only. Never publish a real username, password, token, cookie value, or real URL with a secret in a note or public file.*
