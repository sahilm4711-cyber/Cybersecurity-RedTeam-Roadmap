# How to Learn — Practical Design Notes and Learning Algorithm

## Why this file exists

Most roadmaps tell you *what* to learn. This file tells you *how* to learn it, so you do not get stuck, do not memorise without understanding, and do not quit after two weeks.

A professional hacker is not made by reading 100 pages. A professional is made by a repeatable method: understand, practise in a lab, break it down, write it, explain it, review it. This file designs that method for you, step by step.

---

## Practical design note 1 — Learning is a design, not luck

Think of learning like building a house:

- **Foundation first:** Fundamentals, Linux, networks. Without foundation, advanced topics collapse.
- **One brick at a time:** One topic, one lab, one note page. Not ten topics in one day.
- **Check each brick:** Can you explain it? Can you do it again tomorrow without notes? If not, the brick is not set yet. Do it again, do not add more bricks on top of a weak one.
- **Write the blueprint:** Your notes are the blueprint. If you cannot find a note later, you will rebuild the same brick again and again, wasting time.

**Design rule:** Slow is structured. Fast and messy is not fast — it is rework later.

---

## Practical design note 2 — Watching is not learning

Watching a video or reading a tutorial feels easy. Your brain says "I understand this". Then you close the video and cannot do it alone. That is normal. Understanding by watching is shallow. Understanding by doing is deep.

**Test yourself, honestly, after every topic:**

1. Close all notes and videos.
2. Try the lab task alone.
3. Explain what you did, out loud, in simple English.

If you can do all three, you learned it. If you cannot, you only watched it. Go back to the lab, not to another video.

---

## Practical design note 3 — One tool deep beats ten tools shallow

Beginners often collect tool names: "I know Nmap, Burp, Metasploit, Wireshark, Hydra, John, ..." and cannot explain one tool's output fully.

A professional approach is opposite:

For **one** tool, learn:
- What it is, in one sentence.
- Why it is used.
- When to use it, and when NOT to use it.
- What its output means, line by line.
- One safe lab example, with test data only.
- Two common mistakes.

When you can do that for one tool, the next tool becomes easier, because you already know how to learn a tool. Depth first, breadth later.

---

## Practical design note 4 — Notes are proof, not decoration

Your notes have two jobs:

1. **Help future you:** In three months, future you will forget details. Good notes let future you rebuild a lab in minutes, not days.
2. **Prove your work:** Employers, interviewers, and even your future self trust written proof more than "I watched a course". A GitHub repo with clear notes and reports is proof.

**Bad notes:** Copied text, no examples, no output meaning, no mistakes noted.
**Good notes:** Your own words, your own lab example, your own output, your own mistakes, and your own questions.

---

## The Learning Algorithm — full, step by step

Use this algorithm for every topic in this roadmap. Do not shortcut it. Shortcuts here create weak foundations later.

```
ALGORITHM: LEARN_ONE_TOPIC

INPUT: A topic from the roadmap, for example "Linux permissions"
OUTPUT: Notes + lab proof + ability to explain it tomorrow

STEP 1 — UNDERSTAND IN SIMPLE WORDS (15-30 minutes)
  1.1 Read the concept file slowly, once.
  1.2 Write, in your own words: "This topic is about ______. It works by ______."
  1.3 If you cannot write those two sentences, read again, slower.
      Do not move to Step 2 yet.
  1.4 List 3 questions you still have. Questions are good — they show what to test in lab.

STEP 2 — SEE ONE EXAMPLE (10-20 minutes)
  2.1 Look at the lab example in the file.
  2.2 For that example, write:
      - Input: what goes in?
      - Process: what happens inside?
      - Output: what comes out?
      - Meaning: what does the output tell us about security?
  2.3 Ask: "What would change if I changed one input?"
      Write your guess before you test it. Guessing trains logic.

STEP 3 — DO IT IN YOUR OWN LAB (30-60 minutes)
  3.1 Open your own lab only — your Kali VM, or a legal lab platform.
      Never test on a real system, even "just to see".
  3.2 Type the example yourself. Do not copy-paste blindly.
  3.3 Run it with test data only. Test passwords like hello123, test files you made yourself.
  3.4 Save the output in a file or in your notes, with date and lab name.
  3.5 If it fails, do NOT skip to the next topic. Go to the Debug Algorithm below.

STEP 4 — BREAK IT DOWN (20-30 minutes)
  4.1 Change ONE small thing and run again.
      Example: change a permission from 644 to 600, change a port, change one input.
  4.2 Note: What changed? What stayed the same? Why?
  4.3 Change it back, and change a different thing. Repeat twice.
  4.4 This step is where logical thinking grows:
      - Inputs: what can go in?
      - Trusted parts: what does the system trust without checking?
      - Failure points: where can it break, or where is it weak in lab design?

STEP 5 — WRITE NOTES IN YOUR OWN WORDS (20-30 minutes)
  5.1 Use the Note Template below, every time, same structure.
  5.2 Write in simple English. If a sentence is too complex, break it into two simple sentences.
  5.3 Include: your lab example, your output, your mistakes, and your questions.
  5.4 If your notes are only copied text, rewrite the concept section in your own words now.

STEP 6 — EXPLAIN IT IN ENGLISH (5-10 minutes)
  6.1 Record yourself, or explain to an imaginary friend, for 2 to 5 minutes.
  6.2 Structure: "Today I learned ______. It works like ______. In my lab I did ______. The output was ______, which means ______."
  6.3 Listen back. If you hear "um, I don't know why", that part needs Step 1 again.
  6.4 This step builds communication skill, which is a red-team job skill, not extra homework.

STEP 7 — REVIEW NEXT DAY (10 minutes)
  7.1 Next morning, read yesterday's notes for 10 minutes only.
  7.2 Try the lab example again WITHOUT looking at notes first.
  7.3 If you can do it: mark the topic DONE in your checklist.
      If you cannot: repeat Steps 1, 3, and 5 for that topic today.
      That is not failure. That is the algorithm working.

END ALGORITHM
```

**Why each step matters, in design terms:**
- Step 1 prevents memorising without meaning.
- Step 2 prevents blind copying.
- Step 3 creates real memory through doing.
- Step 4 creates logical thinking, not just tool use.
- Step 5 creates proof and future reference.
- Step 6 creates communication skill for reports and interviews.
- Step 7 prevents forgetting, which is the most common reason beginners feel "I learned it last month but now I forgot".

---

## Debug Algorithm — when something fails in lab

Failure in lab is normal. A professional does not panic. A professional debugs in order.

```
ALGORITHM: DEBUG_A_LAB_ERROR

1. READ the full error, slowly, line by line.
   - What file? What line? What command?
   - Copy the exact error into your notes. Do not paraphrase errors — exact text matters.

2. CHECK the basics first:
   - Are you in the right folder? Run: pwd, ls -l
   - Does the file exist? Check ls output, do not assume.
   - Are you using the right command name? python3 vs python, for example.
   - Did you type the name exactly, with correct capital letters? Linux is case-sensitive.

3. MAKE the problem smaller:
   - Try the smallest version of the command that should work.
   - If a long script fails, test one line at a time in your head, then in terminal.

4. READ the documentation for that one command or tool, not a random blog first.
   - Use: command --help, man command, or official docs for the tool.

5. TRY one fix at a time, and note each try:
   - Try 1: ______, Result: ______
   - Try 2: ______, Result: ______
   - If you try five fixes at once, you will not know which one worked.

6. ASK a specific question, if you are still stuck:
   - Bad question: "It is not working, help."
   - Good question: "I ran [exact command] in [exact folder], I expected [expected output], I got [exact error], I already tried [try 1, try 2]. What should I check next?"
   - Specific questions get specific, fast answers.

7. SAVE the solution in notes, with the exact error and exact fix.
   - Future you will meet the same error again. Good notes save hours later.
```

---

## Note Template — use this for every topic

Copy this template into your notes file for each topic. Same structure every time = faster notes, easier review.

```markdown
# Topic: [Topic name]
Date: [YYYY-MM-DD]
Lab: [Your own Kali VM / TryHackMe lab name / etc — lab only]

## 1. Concept in my own words
[Write 2-3 sentences in simple English. No copied text here.]

## 2. How it works — step by step
1. [Step 1]
2. [Step 2]
3. [Step 3]

## 3. Lab example I did
Command / steps I ran (test data only):
```
[paste your exact command here]
```
Output I got:
```
[paste your exact output here]
```
What the output means, line by line:
- [Line 1 means ...]
- [Line 2 means ...]

## 4. What I changed and what happened
- Change 1: [what I changed] -> Result: [what happened] -> Why: [my guess / explanation]
- Change 2: [what I changed] -> Result: [what happened] -> Why: [my guess / explanation]

## 5. Practical design notes
- [Design point 1: why this matters for security]
- [Design point 2: what a professional would check next]

## 6. Common mistakes I made / must avoid
- [Mistake 1 and fix]
- [Mistake 2 and fix]

## 7. Questions I still have
- [Question 1]
- [Question 2]

## 8. My 2-minute explanation (summary)
[Write the script you would say out loud: "Today I learned... It works like... In my lab I did... The output means..."]
```

---

## Write-up Template — for every lab finding

When you finish a lab test and find something (in lab terms), do not just save tool output. Write a finding like a professional would.

```markdown
# Lab Finding: [Short title]
Lab: [Your own lab name, date]
Scope: [What lab system, what methods allowed — in writing, lab only]

## Simple meaning
[What is this problem, in simple English? One paragraph.]

## Why it matters
[If this design existed in a real system, what could go wrong? Keep it in lab terms, honest severity.]

## How I reproduced it in lab
1. [Step 1, exact lab steps]
2. [Step 2]
3. [Step 3]

## Evidence (lab only, private details removed)
```
[paste lab output here, with private details removed]
```

## Fix idea / design improvement
[How would you fix the design? Example: change permission, close port, check input, update software, etc.]

## What I learned
[One paragraph in your own words.]
```

---

## Daily Practice Structure — flexible, not 16 hours non-stop

You do not need to study 16 hours non-stop to learn. You need a repeatable daily structure, with breaks, movement, and review. Adapt times to your day — college, work, and rest matter too.

**A practical daily design (adjust to your life):**

- **Morning block (60-90 minutes, when fresh):**
  - 10 min review yesterday's notes.
  - 40-60 min new topic: Steps 1 to 3 of the Learning Algorithm.
  - 10 min write notes for today.

- **Midday / library block (light, 30-60 minutes, if free):**
  - Read one write-up or documentation page, in simple English.
  - Note one logical idea: inputs, trusted parts, failure points.
  - Do NOT start a new heavy lab here if you are tired. Light reading is enough.

- **Evening hands-on block (60-90 minutes):**
  - Lab practice: Steps 3 to 4 of the Learning Algorithm.
  - Finish today's notes: Step 5.
  - Save any report or output in your portfolio folder.

- **Evening English block (10-15 minutes):**
  - Record your 2-5 minute explanation: Step 6.
  - Listen once, note one sentence to improve tomorrow.

- **Movement break rule:**
  - Every 50 minutes, stand, walk, stretch for 5-10 minutes.
  - Long sitting days cause pain and foggy thinking. Movement is part of the design, not a break from learning.

**Weekly design:**
- Monday to Friday: one topic per day, using the algorithm above.
- Saturday: one complete lab engagement, start to finish, with a write-up.
- Sunday: review week, rewrite weak notes, plan next week, rest. No new heavy topic on Sunday if you are tired.

**If you miss a day:** Do not try to do two days in one day next day. Just continue from where you stopped, with a 10-minute review first. Consistency beats catching up in a rush.

---

## Progress Tracking — how to know you are improving

Track these, weekly, in a simple file or checklist:

- [ ] Topics completed this week (with notes + lab proof, not just watched).
- [ ] Lab write-ups completed this week.
- [ ] English explanations recorded this week.
- [ ] GitHub commits this week (big, detailed, not tiny).
- [ ] Questions answered, and questions still open.

**Signs you are improving (real signs, not feelings):**
- You can do last week's lab again without notes.
- You can explain a topic to someone else in simple English.
- You can read a tool's output and explain each line, not just the last line.
- You can find your own mistakes faster, using the Debug Algorithm, without panicking.
- Your notes are getting clearer and shorter, because you understand more, not because you are skipping details.

**Signs you need to slow down (also normal):**
- You are copying notes without understanding.
- You cannot explain yesterday's topic today.
- You are jumping to advanced tools to feel progress.
- Fix: go back one phase, repeat the Learning Algorithm for the weak topic, and do not add new topics until that one is solid.

---

## How this repo itself is designed

This repo follows the same design rules it teaches:

- **Structured:** Every file follows the same sections, so you can predict and review fast.
- **Big, not shortcut:** Each commit is detailed, with practical design notes and algorithm, not a list of names.
- **Simple English first:** Complex words are explained when they first appear, in simple words.
- **Lab only:** Every example is for your own lab or a legal lab platform, with test data only.
- **Proof over claims:** Each phase ends with a checklist and a proof item you should save, not just a claim that you "learned" it.

If a file in this repo ever feels like a shortcut, that is a design bug. Note it, and fix it in the next commit, with more explanation, more lab example, and more design notes — not less.

---

## Start here, today

1. Read `01-roadmap/overview.md` fully.
2. Do Phase 0 checklist from that file.
3. Use the Note Template above for your first topic.
4. Record your first 2-minute explanation.

One topic, one lab, one note page, one explanation. Then repeat. That is the whole system. No shortcuts, and no rush — structured progress, one big step at a time.

---

*Educational use only. All practice must be on systems you own or have written permission to test.*
