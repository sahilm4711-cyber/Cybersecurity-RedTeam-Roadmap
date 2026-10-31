# Visual Roadmaps

Two diagrams live in this folder. They are maps, not the work itself. Look at a diagram to see where you are and what comes next, then go and do the reading and lab work behind that box. Looking at the picture is not learning it. Doing the lab task from memory, and explaining it without notes, is.

Lab only, throughout. Your own machine, your own private lab, test data only. Never test real sites, real networks, or anyone else's machine.

## The Red Team diagram

File: `red-team-roadmap.svg`. Open it on GitHub and zoom in. It reads top to bottom, in order, and the order is the point. Fundamentals before tools, Linux before anything that looks like hacking, networks before web work. Skip a phase and the next one will feel like magic — which usually means you missed something underneath it.

Phase 0 is mindset, ethics, and lab: red versus blue versus purple, permission and scope, a working Kali VM, and a notes system you actually use. Phase 1 is computer and Linux basics: how a machine is put together, the filesystem, daily commands, permissions and processes. That maps to `02-fundamentals/`. Phase 2 is networking — IP and ports, DNS, TCP and UDP, HTTP and HTTPS, and the tools that let you look at your own lab network — mapping to `03-networks/`.

Phase 3 is web and OWASP, kept simple and lab-bound, in `04-web/`. Phase 4 is systems and Active Directory: Windows and Linux systems, users and groups, and a small private lab map you drew yourself. Phase 5 is red team tools in the lab: recon, web tools, and AD tools, used inside a scoped lab engagement with findings and fix ideas. Phase 6 is operations and job prep: an engagement plan, a professional report, a portfolio, and interview prep. The proof for that last phase is concrete — a few polished reports and a project you can explain in three minutes, not a list of tools you once ran.

Work one box at a time. Finish it properly, then move down. If you cannot do the box's lab task without looking, you have not finished it, however many times you have read about it.

## The DSA diagram

File: `data-structures-algorithms-roadmap.svg`. Same style, different job. DSA here is a supporting skill, roughly a fifth of the path, not the main road. It earns its place because it explains things you keep meeting in cyber work: how data is stored, how memory works, and how to think through a problem in steps.

Start with Python — it is already the language of your password lab and system audit work, and most red team tooling is Python. Then programming fundamentals: variables, loops, functions, and basic object-oriented ideas. If you cannot write a loop and a function cleanly, DSA will feel hard, and that is a signal to go back to fundamentals, not to push on.

From there: arrays and strings, linked lists, stacks and queues, hash tables. Then Big-O, just enough to understand why some approaches blow up — why brute-forcing a longer password takes exponentially longer, why sorting a big log before searching it matters. Then sorting and searching, trees, and graphs. Trees show up everywhere once you notice them: the filesystem is a tree, a web page's structure is a tree. Graphs show up too: a network is a graph, an Active Directory lab is a graph, and the path-finding ideas in BFS and DFS are how you read a lab map.

Learn the pattern behind each topic, not a pile of memorised problems. Two pointers, sliding window, recursion, and the idea of dynamic programming are worth knowing as ideas. Skip the deep end for now — segment trees, advanced dynamic programming, deep tries, complex graph algorithms in full detail. Come back to those only if a real project needs them.

A useful rule: learn DSA alongside Python and labs, in small pieces, two or three times a week for about half an hour. The moment DSA starts eating all your time, go back to Linux, networks, and lab work. That is the main path.

## Using both together

The red team diagram is the main path, week by week. The DSA diagram is the side path you dip into when Python or a red team topic needs it — hashing, graphs, trees, log searching. A workable week looks like this: red team phase work on weekdays, a short DSA box with a small Python example a couple of times a week, a complete lab engagement with a write-up on Saturday, and a Sunday review where you check which boxes you can now explain without notes and pick next week's focus.

Do not try to finish both diagrams in a month. Steady phases over nine to twelve months, with DSA basics alongside, beats a rushed month that leaves gaps everywhere. When you finish a box, mark it in your own notes. Your personal map — what you actually did — is more useful than any generic diagram.

## Files in this folder

`red-team-roadmap.svg` is the red team diagram. `data-structures-algorithms-roadmap.svg` is the DSA diagram. `index.html` is a local viewer for both; GitHub will not run it as a website, but the SVGs open directly there. This file explains how to read them.

These diagrams are original to this repo, drawn in roadmap style, not copied from another image. Educational use only, lab only.
