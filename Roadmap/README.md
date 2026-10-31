# Roadmap — Visual Roadmaps

## What is in this folder?

This folder contains **visual roadmaps in the same style as the roadmap diagram you sent**: yellow boxes for main topics, light-yellow boxes for sub-topics, blue lines showing learning order, top to bottom.

These pictures are **maps, not shortcuts**. Each box maps to a detailed text file in this repo. Read the picture first to see the order, then read the text file for that box to learn it deeply, with practical design notes, algorithm, lab examples, and checklists.

**Safety rule for both roadmaps:** Lab only. Practise only on systems you own or have written permission to test. Use test data only. Never test real websites, networks, or other people's computers.

---

## 1. Red Team Roadmap — Visual

**File:** `red-team-roadmap.svg`
**Open it on GitHub:** Click the file name above, GitHub will show the diagram directly. You can zoom in, download it, and share it.

**How to read it, top to bottom:**

1. **Phase 0 — Mindset, Ethics & Lab**
   - Red vs Blue vs Purple, Permission & Scope, Kali VM Lab Setup, Notes System
   - Maps to: `README.md` safety rule, `01-roadmap/overview.md` Phase 0, `01-roadmap/how-to-learn.md`
   - Proof: Working lab + scope note + first notes page.

2. **Phase 1 — Computer & Linux**
   - Computer Basics, Linux Filesystem, Linux Commands, Permissions & Processes
   - Maps to: `02-fundamentals/computer-basics.md`, `02-fundamentals/linux-filesystem.md`, `02-fundamentals/linux-commands.md`
   - Proof: System audit report from your own machine, permissions you can explain.

3. **Phase 2 — Networking**
   - IP & Ports, DNS & TCP/UDP, HTTP & HTTPS, Network Tools
   - Maps to: `03-networks/network-basics.md`, `03-networks/network-tools.md` (coming in next big commits)
   - Proof: Lab network diagram with IPs and ports, localhost port check you can explain.

4. **Phase 3 — Web & OWASP**
   - Web Basics, Requests & Cookies, OWASP Top 10 Simple, Lab Write-ups
   - Maps to: `04-web/web-basics.md`, `04-web/owasp-simple.md` (coming)
   - Proof: 3+ lab write-ups you can explain without reading word for word.

5. **Phase 4 — Systems & Active Directory**
   - Windows Basics, Linux Systems, AD Users & Groups, AD Lab Map
   - Maps to: Phase 4 files in `01-roadmap/overview.md`, plus AD files coming later.
   - Proof: Small private lab diagram, permissions report from lab.

6. **Phase 5 — Red Team Tools & Lab**
   - Recon in Lab, Web Tools in Lab, AD Tools in Lab, Lab Findings
   - Maps to: `05-tools/` files coming later.
   - Proof: 3+ complete lab engagements with scope, findings, and fix ideas.

7. **Phase 6 — Operations & Job Prep**
   - Lab Engagement Plan, Professional Report, Portfolio & GitHub, Interview Prep
   - Maps to: `06-practice/` and `07-resources/` files coming later.
   - Proof: 3 polished reports, portfolio README, 3-minute project explanation.

**Practical design note — why this order:**
Fundamentals before tools, Linux before hacking, networks before web attacks, one tool deep before ten tools shallow, notes before certificates, reporting as a core skill not extra work. If you jump a phase, the next phase will feel like magic. If you follow the order, each phase makes the next one logical.

**Algorithm to use this diagram:**
```
1. Look at the diagram, find your current phase.
2. Read the text file for that phase fully.
3. Do the lab tasks for that phase, on your own machine only.
4. Pass that phase's checklist in the text file.
5. Only then move to the next box down in the diagram.
6. If you cannot pass the checklist, stay on that box and repeat the learning algorithm from 01-roadmap/how-to-learn.md.
```

---

## 2. Data Structures & Algorithms Roadmap — Visual (Supporting Skill)

**File:** `data-structures-algorithms-roadmap.svg`
**Open it on GitHub:** Click the file name above, GitHub will show the diagram directly.

**Why DSA is in a Red Team repo at all:**
DSA is **not** the main Red Team path. It is a supporting skill, about 20% of your path, that makes the main 80% stronger. It helps you understand how programs store data, how memory works, how to write better tools, and how to think logically.

**How to read it, top to bottom:**

1. **Pick a Language — Python first for cyber**
   - Python, Language Syntax, Control Structures, Functions & OOP.
   - Why Python first: your password lab and system audit are already in Python, and most red-team tools and scripts use Python.

2. **Programming Fundamentals — Logic before DSA**
   - Variables & Loops, Pseudo Code, Functions, OOP Basics.
   - This is the logic layer: if you cannot write a loop and a function clearly, DSA will feel hard. Master this first.

3. **Basic Data Structures — How data is stored**
   - Array & String, Linked List, Stack & Queue, Hash Table.
   - Cyber link: String = web input, Hash Table = password hashing and fast search, Stack = function calls and memory, Array = ordered data.

4. **Algorithmic Complexity — How fast is it?**
   - Big-O Notation, Time Complexity, Space Complexity, Common Runtimes.
   - Cyber link: Why brute-force time grows exponentially with password length, why sorting logs matters, why a scanner design matters.

5. **Sorting & Searching — Find it fast**
   - Bubble & Merge Sort, Quick & Selection Sort, Linear Search, Binary Search.
   - Cyber link: Searching a huge log file, sorting findings by severity, binary search only works on sorted data — that condition matters.

6. **Tree Data Structures — Branching data**
   - Binary Trees, Binary Search Trees, Tree Traversal, Heap Basics.
   - Cyber link: Filesystem is a tree, web page DOM is a tree, directory traversal follows tree logic.

7. **Graph Data Structures — Connected data**
   - Graph Basics, BFS (Breadth First), DFS (Depth First), Shortest Path Idea.
   - Cyber link: Network is a graph, Active Directory is a graph, BloodHound in lab draws AD as a graph. BFS/DFS is how you find a path in a lab map.

8. **Advanced & Problem Solving — Think in patterns**
   - Two Pointers, Sliding Window, Recursion, Dynamic Programming Idea.
   - Learn the idea first, not deep competitive problems. Patterns help you solve new problems, not memorise old ones.

9. **DSA in Cyber Security — Supporting skill, not main path**
   - Hashing & Passwords, Graph for AD & Networks, Tree for Filesystem, Log Search & Sorting.
   - This box is the bridge: it shows where each DSA topic actually appears in cyber work, in lab terms.

**What to learn for red team, and what to skip for now:**
- **Learn now (basics):** Array, String, HashMap/Dictionary, Stack, Queue, Linked List basics, Binary Tree basics, Graph basics (BFS/DFS idea), Linear + Binary Search, Sorting ideas, Big-O idea, Hashing idea.
- **Skip for now:** Segment Tree, advanced Dynamic Programming, deep Trie, complex graph algorithms in full detail. Learn them later only if a specific job or project needs them.
- **Do not stop red team labs for DSA alone.** Learn DSA alongside Python and labs, one small topic at a time, with small lab examples. If DSA starts taking all your time, return to Linux/network/lab work — that is your main path.

**Algorithm to use the DSA diagram:**
```
1. Pick one box, top to bottom, in order. Do not jump to Advanced first.
2. Learn it in simple words, with one small Python example in your own lab.
3. Write one note: what it is, how it works, one cyber example, one mistake to avoid.
4. Explain it in 2 minutes in simple English.
5. Only then move to the next box down.
```

---

## 3. How to use both diagrams together

- **Main path = Red Team diagram.** Follow it phase by phase, every week.
- **Supporting path = DSA diagram.** Use it when you are learning Python and tools, or when a red-team topic needs a DSA idea (for example, when you meet hashing, graphs, or trees).
- **Weekly design example:**
  - Monday to Friday: Red Team phase work (Linux, networks, web, labs).
  - 30 minutes, 2-3 times a week: one DSA box, with a small Python example, in your lab.
  - Saturday: one complete lab engagement, with a write-up.
  - Sunday: review both diagrams, check which boxes you can now explain without notes, plan next week.

Do not try to finish both diagrams in one month. Finish Red Team phases steadily over 9-12 months, and finish DSA basics alongside, slowly. Structured progress beats rushed progress.

---

## Files in this folder

- `red-team-roadmap.svg` — Red Team visual roadmap, yellow boxes, blue connectors, top to bottom.
- `data-structures-algorithms-roadmap.svg` — DSA visual roadmap, same style, supporting skill for cyber.
- `index.html` — Interactive viewer for both diagrams, with links to text files. Open it locally in a browser after pulling to Kali. GitHub does not run HTML as a website, but the SVG files above open directly on GitHub.
- `README.md` — This file. How to read both diagrams, box-by-box mapping, practical design notes, and algorithms.

---

## Practical design notes for visual roadmaps

1. **A picture is a map, not the learning itself.** A diagram shows order and connections. Learning happens when you read the text file, do the lab, write notes, and explain it. Do not just look at the picture and say "I learned it".
2. **Order matters more than speed.** The blue lines are not decoration. They show dependency: you need Phase 1 before Phase 5 makes sense, and you need Basic Data Structures before Advanced Problem Solving makes sense.
3. **One box at a time.** Pick the next box you have not passed yet, learn it fully, pass its checklist, then move down. Jumping ahead creates gaps that show up later as confusion.
4. **Update the map as you learn.** When you finish a box, mark it in your own notes (not in this repo, unless you want to fork it for personal tracking). Your personal progress map is more useful than a generic map, because it shows what *you* actually did.

---

*Original diagrams created for this repo, in roadmap style, not copied from any other roadmap image. Educational use only. All practice must be on systems you own or have written permission to test.*
