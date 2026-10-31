# Web Tools

You know web basics, OWASP risks, and the tool mindset. Now you learn web tools — the tools you use to understand, check, and test a lab website you built yourself.

Web tools don't make you a tester by themselves. They make visible what is already there: requests, responses, headers, cookies, and design flaws in your own lab app. The skill is reading those things, tracing them, and writing a clear note about them.

Everything here is for a lab website you built yourself, or a deliberately vulnerable lab app installed only on your own machine. Never use a web proxy, scanner, or tester against a real website, login, or API without written permission. Use test data only — test usernames like `test-user`, test passwords as placeholders like `<test-password>`, and sample pages you created yourself. Never put a real username, password, token, cookie value, or real URL with a secret in a note, screenshot, or public file. Use placeholders: `<lab-url>`, `<test-password>`, `<session-id>`, `<token>`.

A web tool result is not permission to exploit a finding. In lab, a finding means: write a note, fix the design in your own lab app, and re-test your own fix.

## Browser developer tools: your first web tool, already installed

Developer tools are built into your browser. They show you, for a page you open: network requests, HTML structure, CSS, JavaScript, cookies, storage, and console messages.

Learn these before any other web tool. They show you the same requests and responses every other web tool shows, but with no extra setup, and with no risk when you use them on your own lab page. They are the right tool every time you open a lab page to trace what it actually does — especially when a lab page doesn't work as expected, or when you want to understand a lab login, a lab form, or a lab API call.

On your own lab page only: open it at `http://127.0.0.1:<lab-port>/`, open developer tools, Network tab, and reload once. Click the main HTML request and read the method, status code, request headers, response headers, and body, in your own words. Click a CSS or JavaScript request and note why the browser needed it. Open the Application / Storage tab, find cookies for your lab site only, and note the cookie names — not values — and whether HttpOnly is present. Close developer tools when you're done. Don't copy cookie values or tokens into a note.

Read the Network tab for what it is: one row is one HTTP request, with method, path, status, size, and time. Status 200 means that request succeeded, for that resource, at that time. A request with a large size or long time is a clue, not a verdict — it may be a large image, a slow lab service, or a missing cache. Check the resource itself, in lab. Console errors are clues about JavaScript or loading problems in your lab page, not proof of a security flaw.

Two things to watch out for. Developer tools can't "hack" a page — they only show what the browser already received, and changing something in them only changes your own browser view, not the server. And clear the Network tab before reloading, or you'll read old requests as if they were new.

## `curl`: the browser without the page

`curl` makes HTTP requests and shows the response, without rendering a page. You met it in network tools. As a web tool, its value is that it shows the raw request and response, and lets you test one lab endpoint at a time. A browser hides details for good user experience. `curl` shows them: status line, headers, body, and, with the right flags, the request it sent. That is how you actually learn HTTP.

Against your own lab web service only, one endpoint at a time, with test data only:

```bash
curl -i http://127.0.0.1:<lab-port>/
curl -i http://127.0.0.1:<lab-port>/lab-notes
```

The `-i` flag includes response headers, not just the body. The first command asks for the lab home page. The second asks for a lab notes page you created yourself, if you have one.

Read it line by line. `HTTP/1.1 200 OK` means that lab endpoint returned success for that request. `Content-Type: text/html` means the body is HTML; `application/json` means it is JSON. `Set-Cookie: session_id=<...>` means the lab server asked the browser to store a session cookie — in a note, write `<session-id>`, not the real value. The body is the actual page or data. Read it as data, not as code, unless it is a page you wrote yourself in lab.

Don't send a real password or token with `curl` and then share your command history or a screenshot publicly. And don't mistake a `curl` success for security — it only means the service responded to that one request. Also, don't follow redirects blindly or download a file without checking what it is first, even in lab.

## Web proxies in lab: seeing requests in the middle, on your own traffic only

A web proxy, in lab, sits between your browser and your lab server. Your browser sends requests to the proxy, the proxy forwards them to the lab server, and it shows you each request and response, so you can read, pause, or change them — for your own lab traffic only.

A proxy makes the invisible visible. A browser hides some steps. A proxy shows every request, in order, with headers and body, so you can trace a lab login, a lab form, or a lab API call, step by step.

The lab setup, in outline: start your own lab web service with test data only, on localhost. Start a lab proxy tool on your own machine, following its own documentation, for lab traffic only. Configure your own browser to use that proxy for lab traffic only, if the tool requires it. Open your own lab page once, and read the proxy log: one request, method, path, status, headers, body, in order. If your proxy allows it, pause one lab request, read it, and release it unchanged, to see how pausing works. Don't change a request to bypass a lab check unless you are deliberately testing your own lab fix, with your own data, and you write a note about what you changed and why. When you're done, turn the proxy off, restore your browser settings, and stop the lab service.

A proxy log is a list of HTTP requests and responses, in order, for your own lab traffic — like the Network tab, but with more detail about headers and body. A request you changed in a proxy is not what the browser originally sent, so note in your lab notes whether a request was original or changed, and what you changed, with test data only. And a proxy does not prove a flaw. It proves what requests were made, and what responses came back, in that lab session.

The hard lines here are simple. Leaving a proxy on after lab, so all your browsing goes through it, is a mistake — turn it off when lab ends. Intercepting traffic that is not yours is the line between lab learning and a privacy violation. Never set a proxy to intercept traffic from other people, or on a shared, school, or company network, and never use one to change requests to a real website.

## Web scanners in lab: automated checks, with limits

A web scanner automatically sends many requests to a web app, looking for common design flaws: missing headers, known patterns, error messages, and so on. Think of it as a checklist run by a program, not a human tester.

A scanner can find obvious, common issues in your own lab app quickly, so you can fix them and learn what they look like. It cannot understand your lab app's logic, its permissions, or its business rules — only a human tracing requests can do that.

Run one only against a lab web app you built yourself, or a deliberately vulnerable lab app on your own machine, after you have written down why you are scanning it, and only with a small, slow scan, one lab app at a time. Never run a web scanner against a real website, login, or API. Scanners send many requests, they are noisy, they are logged, and they can cause damage — for example by creating, changing, or deleting data through forms. Running one without written permission is unauthorised testing, even if you "only wanted to check".

The lab rhythm: build or install the lab app with test data only, write down in one sentence what you want the scanner to check, run it once, slowly, with its default safe settings for lab if it has them. Read each finding, one by one, and for each ask: is this a real design flaw in my lab app, or a false positive? What request caused it? What would a safe design do instead? Fix one finding in your own lab app, re-run the scanner once for that finding only, and note whether it is gone. Stop the lab app and the scanner when you're done. Don't publish the scanner report with real data — write a summary with placeholders.

A scanner finding is a clue, not a verdict. It says: "This lab app behaved in a way that matches a common pattern." It does not say: "This app is exploitable" or "This app is safe." False positives are normal — a scanner does not know your lab app's intent, so you must check each finding by tracing the request and response yourself, in lab. And no findings does not mean secure. A scanner only checks patterns it knows, with inputs it tries. It does not check your app's logic, permissions, or custom flaws.

Don't copy a scanner report into a resume or public file without understanding each finding, and don't run aggressive settings without understanding what they do — they can change or delete data, even in lab. "Free" and "common" are not permission.

## Putting it together: one lab web check

When you have a lab app to check, work in this order: start the lab app on localhost with test data only. Open it in a browser with developer tools Network tab open, reload once, and trace the main request. Run `curl -i` once against the same lab URL, and compare the status and headers with the browser trace — do they match? If you have a lab proxy, read its log for that one lab session, in order, and note any request you don't understand. If you have a lab scanner, run it once, slowly, against that one lab app only, read each finding, and check it by tracing the request yourself. For each real flaw you find in your own lab app, write a note: what it is, what request caused it, what a safe design does instead, and how you fixed it in lab. Re-test your fix once, with the same tool, for that finding only. Then clean up: stop the lab app, turn off the proxy, close developer tools, and don't publish real tokens, cookies, or data.

That is the web version of the tool mindset from `tools-overview.md`: question first, smallest tool first, trace first, note first, fix in lab, re-test once. A professional web tester follows this pattern, with written permission, on a real scope, later. A learner follows it now, in lab, to build the habit.

One last thread: a web tool can change data, even in lab, if it submits a form or follows a link that deletes something. Use test data, and be ready to rebuild your lab app if a test changes it. And close everything you opened — a lab server left running, or a proxy left on, after lab ends is how a tidy session turns into a leak.

---
*All examples here are for your own machine or your own private lab only. Use test data only. Never publish a real username, password, token, cookie value, or real URL with a secret in a note or public file.*
