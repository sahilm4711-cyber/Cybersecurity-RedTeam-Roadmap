# How to Learn

Most roadmaps tell you what to learn. This one tells you how, so you don't get stuck, memorise without understanding, or quit after two weeks. A professional hacker isn't made by reading a hundred pages, but by a repeatable method: understand, practise in a lab, break it down, write it, explain it, review it.

## Learning is a design, not luck

Think of it like building a house. Fundamentals, Linux, and networks are the foundation — without them, advanced topics collapse. One topic, one lab, one note page at a time is one brick at a time, not ten topics in a day. Check each brick: can you explain it, and can you do it again tomorrow without notes? If not, the brick isn't set — do it again before stacking more on a weak one. Your notes are the blueprint; if you can't find a note later, you'll rebuild the same brick again and again.

## Watching is not learning

Watching a video or reading a tutorial feels easy, and your brain says "I understand this." Then you close the video and can't do it alone. That's normal — understanding by watching is shallow; understanding by doing is deep. Test yourself honestly after every topic: close all notes and videos, try the lab task alone, and explain what you did out loud in plain English. If you can do all three, you learned it. If you can't, you only watched it. Go back to the lab, not to another video.

## One tool deep beats ten tools shallow

Beginners collect tool names and can't explain one tool's output fully. Do the opposite: for one tool, learn what it is in one sentence, why it's used, when to use it and when not to, what its output means line by line, one safe lab example with test data only, and the mistakes you made with it. Once you can do that for one tool, the next gets easier, because you already know how to learn a tool. Depth first, breadth later.

## Notes are proof, not decoration

Notes have two jobs. They help future you — in three months you'll forget details, and good notes let you rebuild a lab in minutes instead of days — and they prove your work, because employers and interviewers trust written proof more than "I watched a course." Copied text with no examples, no output meaning, and no mistakes noted is not notes. Good notes are your own words, your own lab example, your own output, your own mistakes, and your own questions.

## The method, in practice

Use this loop for every topic. It looks long on paper; in practice it becomes habit.

Start by understanding in plain words. Read the concept file slowly, once, then write two sentences: what this topic is about, and how it works. If you can't write them, read again, slower, and don't move on yet. List the questions you still have, too — they tell you exactly what to test in the lab.

Next, trace one example before you run anything: what goes in, what happens inside, what comes out, and what the output tells you about security. Guess what would change if you changed one input, and write the guess down before you test it. Guessing trains logic; skipping the guess trains nothing.

Then do it in your own lab — your Kali VM, or a legal lab platform, never a real system, even "just to see." Type the example yourself rather than copy-pasting blindly, run it with test data only, and save the output with the date and lab name. If it fails, don't skip to the next topic; debug it, because an error you walk past is a gap you'll meet again.

Then break it down. Change one small thing and run it again: a permission from 644 to 600, a different port, one different input. Note what changed, what stayed the same, and why. Change it back, change something else, repeat twice. This is where logical thinking grows: inputs, the parts the system trusts without checking, and the failure points where it breaks or where the lab design is weak.

Then write notes in your own words, same shape every time so review stays fast: the concept as you'd say it, how it works, the lab example you actually ran with its output explained line by line, what you changed and what happened, why it matters for security, the mistakes you made, and the questions you still have. If any part is copied text, rewrite that part now.

Then explain it in English for two to five minutes, recorded or to an imaginary friend: what you learned, how it works, what you did in the lab, what the output meant. Listen back. Anywhere you hear yourself saying "I don't know why," that part needs another pass through the understanding step. This isn't extra homework — communication is a red team job skill, and reports and interviews test it directly.

Finally, review the next day. Ten minutes: re-read yesterday's notes, then try the lab example again without looking at them first. If you can do it, the topic is done. If you can't, repeat the understand, do, and write parts today. That's not failure — it's the loop working, and the main defence against "I learned it last month but now I've forgotten it."

Each part earns its place: understanding stops empty memorising, doing creates real memory, breaking it down creates logical thinking, writing creates proof, explaining creates communication skill, reviewing stops forgetting.

## When something fails in the lab

Failure in the lab is normal. A professional doesn't panic; a professional debugs in order.

Read the full error slowly, line by line — what file, what line, what command — and copy the exact error into your notes. Don't paraphrase errors; exact text matters when you're searching or asking for help. Check the basics first. Are you in the right folder? Run `pwd` and `ls -l`. Does the file actually exist, or did you assume it does? Are you using the right command name — `python3` versus `python`? Did you type the name exactly, capitals and all? Linux is case-sensitive, and a surprising number of "broken" labs are a typo.

Then make the problem smaller. Try the smallest version of the command that should work; if a long script fails, test one line at a time, in your head first, then in the terminal. Read the documentation for that one command or tool — `command --help`, `man command`, or the official docs — before a random blog post. Try one fix at a time and note each try and its result; try five at once and you'll never know which one worked. If you're still stuck, ask a specific question: not "it's not working, help," but "I ran this exact command in this exact folder, I expected this output, I got this exact error, and I already tried these two things — what should I check next?" Specific questions get specific, fast answers. Once it's solved, save the error and the fix in your notes. Future you will meet the same error again, and good notes save hours.

## What a good lab write-up looks like

When a lab test turns up something, don't just save tool output. Output is evidence, not a report. A professional write-up says, in plain words, what the problem is and why it matters, with honest severity rather than exaggeration; the exact lab steps that reproduced it; the lab evidence with private details removed; the fix idea — change a permission, close a port, check input, update software; and what you learned, in your own words. That shape works for one finding or a whole engagement, and it's the shape interviewers recognise.

## A daily structure that survives real life

You don't need sixteen hours non-stop. You need a repeatable day, with breaks, movement, and review. Adapt the times to your life — college, work, and rest matter too.

A morning block of sixty to ninety minutes, when you're fresh, is best for a new topic: ten minutes reviewing yesterday's notes, forty to sixty minutes on understanding and doing, ten minutes writing notes. A midday or library block, thirty to sixty minutes if you have it, is for light work: read one write-up or documentation page, note one logical idea — inputs, trusted parts, failure points — and don't start a heavy lab here if you're tired. An evening block of sixty to ninety minutes is for lab practice and finishing today's notes, plus saving any report in your portfolio folder. Then ten to fifteen minutes of English: record your short explanation, listen once, note one sentence to improve tomorrow. Every fifty minutes, stand, walk, and stretch for five to ten minutes — movement is part of the design, not a break from learning.

Across a week: one topic per weekday, one complete lab engagement on Saturday with a write-up, and Sunday for review. If you miss a day, don't try to do two days in one day next day — continue from where you stopped, with a ten-minute review first.

## How you know you're improving

Track weekly, in a simple file: topics completed with notes and lab proof, not just watched; write-ups finished; explanations recorded; commits made; questions answered and still open.

The real signs of improvement aren't feelings: you can do last week's lab again without notes, explain a topic to someone else in plain English, read a tool's output and explain each line, not just the last one, and find your own mistakes faster without panicking. Your notes are getting clearer and shorter — because you understand more, not because you're skipping details.

The signs you need to slow down are normal too: copying notes without understanding, not being able to explain yesterday's topic today, jumping to advanced tools to feel progress. The fix is always the same: go back one phase, run the loop again on the weak topic, and don't add new topics until that one is solid.

## How this repo itself is designed

This repo follows the same rules it teaches: structured so you can predict and review fast, detailed rather than shortcut, complex words explained in plain words when they first appear, every example lab only with test data, and each phase ending with proof you should save, not just a claim that you learned something. If a file here ever feels like a shortcut, that's a design bug — note it, and fix it with more explanation, more lab example, and more reasoning, not less.

## Start here, today

Read `01-roadmap/overview.md` fully, work through Phase 0, use the note shape above for your first topic, and record your first two-minute explanation. One topic, one lab, one note page, one explanation. Then repeat. That's the whole system — no shortcuts, no rush, one solid step at a time.

---

*Educational use only. All practice must be on systems you own or have written permission to test.*
