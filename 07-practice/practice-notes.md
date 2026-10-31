# Practice Notes

You have covered fundamentals, Linux, networks, web, tools, passwords, systems, and safe labs. This file is about the part most people skip: turning what you have read into skill that stays. Reading tells you something exists. Skill is what you can do without looking at the answer, slowly at first, then faster, with fewer mistakes.

Lab only here, as everywhere in this roadmap. Your own machine, your own lab folder, or your own private lab, test data only, generic placeholders like your-user and <test-password> in every note. Never practise on a real system, a school or company system, or someone else's machine without written permission, and never put real credentials, real IPs, or real personal details in a note or a public file.

## See it, do it, explain it

Most learning stalls at the first stage. You read an explanation, follow one example line by line, and it feels clear. That feeling is familiarity, not skill. It fades fast.

Real practice has three stages, in order. First you see it: read and follow one example slowly. Then you do it: close the example and repeat the task from memory, with one small change — a different lab folder, a different test file, a different port in your own lab. Then you explain it: in plain English, without looking, what you did, why, and what the output meant. If you cannot explain it, you have not finished learning it yet, and going back to reading will not fix that. Doing it again will.

Take a concrete session. Say you want to practise permissions. Before you start, write down what you expect: your lab folder at 755, a test file at 644 before the fix and 600 after. That baseline matters, because a command without an expected result is just text on a screen — you cannot tell whether you succeeded. Run `ls -ld` on the folder itself, `ls -l` inside it, then `chmod 600` on the test file, then check again. If the check matches the baseline, good. Then change one thing and do it again from memory, on a second test file. That second run is where the learning actually happens.

Watch out for the trap of only practising the easy part. The place you get stuck is the practice. If you used `ls -l` when you meant the folder itself and got its contents instead of its own permission, that is not a wasted session — that is the lesson, and it is exactly what `ls -ld` is for. Write it down while it is fresh.

## Small to small

The method that works here is the one you already know from learning Python: one small piece at a time, checked, before the next piece. Break a task into the smallest pieces you can — one command, one flag, one check — and do not move on until the current piece makes sense. If a piece fails, stop there. Fix it, or write down exactly where it failed, before pushing forward. Pushing on with a broken piece and hoping the next one fixes it is how a whole session ends in confusion.

Learning a new network command works the same way. Run `ss -tln` on your own machine and say in one sentence what it shows: listening TCP ports. Separate the localhost-only ports from the exposed ones. For one exposed port in your lab, say what lab service it is and whether you need it. Stop a lab service you do not need and re-run the check to confirm it is gone. Only then try to describe the whole audit from memory. Each small check tells you exactly where you stand, which is what makes a long roadmap survivable day after day.

## The note after the session

After a session, write a short private note — about five lines, no more. What one thing did you practise? What did you do, in general terms, naming commands rather than pasting full output? Did the result match your baseline, and if not, why? Where did you get stuck, in one sentence, and what did you learn from it? What one thing will you practise next time?

Short is a feature, not laziness. A five-line note is easy to write, easy to re-read, and hard to accidentally fill with real personal details. A fifty-line note full of tool output is hard to write, hard to re-read, and likely to leak something it should not. Re-read yesterday's note before today's session, in about a minute. That one habit is what turns isolated sessions into a chain, and a chain of small sessions is a roadmap in action.

A note with no stuck point is a note with no lesson. A note with no next step means the next session starts from zero again. And a note you never re-read was not worth writing.

## Failure notes

Keep failure notes separate in your head from success notes, because they teach different things. Success notes tell you what already works. Failure notes tell you where your understanding has a gap, and a gap written down clearly is a gap you can close.

A useful failure note is short: what you tried, what happened, what you think caused it, and what you will try next. Say you start a local web server on 127.0.0.1 and the port is already in use. Your best guess might be that you left an earlier lab server running and skipped cleanup last time. The next step is small: check listening ports on your own machine, stop the old lab server, try again, and clean up properly at the end. Keep the note even after you fix the problem, with the fix written next to it. Over time you will see patterns — the same kinds of failures recurring, and the ones you have genuinely fixed for good.

Do not write "it did not work" and stop there. That is a feeling, not a note. And check your own step first before blaming the tool or the tutorial. Most of the time, a smaller test will show you exactly where your step went wrong.

## Reviewing before skill fades

Skill fades if you do not use it, so review is doing, not re-reading. Re-reading feels easy, and that easy feeling is misleading. Once a week, redo one task from last week from memory, with a small change. Once a month, redo one task from last month's topic and write a fresh short note about what stayed and what faded. Once a month, explain one topic out loud or in writing, without looking. Whatever you cannot explain goes back to practice, not back to reading.

Review one thing well rather than ten things badly. A task you can do after a week, from memory, is a skill. A task you can only do immediately after reading is not yet one.

## Picking what to practise

One thing well beats ten things badly, and this is where most roadmaps quietly fall apart. It is tempting to collect tools — run one recon tool in the lab, then a web tool, then something for AD, and feel busy all evening. Busy is not progress. Depth is. One tool understood properly — what it does, what its output actually means, where it misleads you — is worth more than ten tools you can only run with the flags copied from a page.

So pick one main thing per session and let the session be about that. If you are working on your password checker, the session is about the checker: test data only, a few test passwords, what the strength result means and where it is wrong. If you are working on a system audit, the session is about your own machine: listening ports, folder permissions, a report you can explain line by line. When the session is done, the note should be able to say what changed in your understanding, not just what you ran.

The same thinking applies across weeks. A week with one main topic, practised in small sessions and then used in a small project, leaves something behind — a script, a note, a write-up. A week with a new topic every day leaves familiarity and very little else. Familiarity is comfortable, which is why it is dangerous: it feels like learning right up until someone asks you to do the thing without notes.

Watch out, too, for practising in a way that avoids the hard part. If you always test with the same easy test file, the same simple password, the same lab setup that already works, you are rehearsing success, not learning. Change one variable at a time and watch what breaks. That is where the reasoning lives: why did this input give that result, what would have to change for a different result, and what does that tell you about how the thing works underneath?

## Write-ups are practice, not paperwork

A short write-up after a lab task is not admin work tacked onto the end. It is the explanation stage, written down. A few paragraphs are enough: what the lab setup was, in generic terms, what you tried, what you found, and what you would fix or do differently. Use a placeholder lab address like 127.0.0.1 or <your-lab-ip> and a placeholder site like example.com if you need an example at all, never a real target.

Writing forces the gaps out into the open. While you are running commands, it is easy to skim past something you half-understood. When you have to write it in order — this is what I set up, this is what happened, this is why I think it happened — the half-understood part shows up as a sentence you cannot finish. That sentence is your next practice question. Over months, those write-ups become the portfolio: proof, in your own words, that you did the work and understood it, rather than a list of topics you once read about.

## Honesty, in private notes and in public

Good practice is honest practice. Write down what you actually did, what actually failed, and what you do not understand yet, without exaggerating and without hiding failures. This matters beyond learning. A public claim should match a private note backed by a task you can do from memory in the lab. Claiming a skill you have only read about will fail in front of other people — in an interview, a lab test, or a real project — and it will fail at the worst moment. A note that says you tried something, got stuck at a specific point, and are practising that point next is a note you can defend, and it shows how you learn, which is what a good employer or mentor actually wants to see.

A lab note, honestly written, is also more impressive than any note that breaks a rule to look impressive. Practising on a real system to make a note look better is not ambition; it is a shortcut that costs trust. Keep the practice in your own lab, clean up after each session — stop lab services, check your notes for real details — and let the notes speak for themselves.

Next, `daily-practice.md` turns this way of practising into a daily and weekly rhythm that fits around college and real life without burning out.
