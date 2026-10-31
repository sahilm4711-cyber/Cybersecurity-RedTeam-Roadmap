# Password Tools

You know Linux, networks, and the web. Now password tools — not to break other people's passwords, but to understand what makes a password strong, how a good system stores it, and how to check your own test passwords safely.

If you can explain length, character types, entropy in plain terms, hashing, and salting, and you can check a test password without ever using a real one, you understand this topic. If you only know a tool name, you don't.

Safety first, and it is non-negotiable: use test passwords only — invent a test word for lab, never a real password you use anywhere else. Never test, check, crack, or try to guess a real person's password, a real account, or a password file you don't own. Never put a real password, a real hash from a real system, or a real username in a note, screenshot, or public file. Use placeholders: `<test-password>`, `test-user`, `your-user`.

## What a password actually is

A password is a secret you know, that a system checks to confirm you are who you claim to be. It is one factor of authentication: something you know. The other factors — something you have, like a phone or a key, and something you are, like a fingerprint — are later topics, not this file.

A password system has three parts, and a flaw in any one of them breaks the whole thing: creation (how does a user choose a password, and is it checked for weakness?), storage (plain text — never — or a hash, covered below?), and checking (how does the server compare what you typed with what it stored, without leaking which part was wrong, and without allowing unlimited guesses?). A red-team learner, later, checks all three parts in a lab app they built themselves — not in a real app.

## What makes a password strong

In order of importance:

**Length matters most.** A longer password takes much longer to guess, even if it is simple words. A 16-character password made of four random words is, in plain terms, much stronger than an 8-character password with symbols, because the guessing space grows with every character. Think of it like a combination lock: each extra character adds another wheel, and each wheel multiplies the number of combinations. That is why length grows strength so fast, and why a short password, no matter how clever, is still a small lock.

**Unpredictability matters next.** A password that is a common word, a name, a date, or a pattern like `123456` is easy to guess, because guessing in a lab starts with common lists first. A password that is random, or made of unrelated words you chose, is harder to guess.

**Character types help, but they don't fix a short password.** Upper case, lower case, numbers, and symbols increase the guessing space, but only a little compared to adding length. A short password with symbols is still short.

And a few habits that matter as much as strength: don't reuse the same password across sites — if one site leaks it, every site using it is at risk. Don't use a password that is your name, a pet name, a birthday, or a common word with a number at the end — that kind of weak choice is exactly what a lab checker should reject. And don't share a password in a note, screenshot, or chat, even a test one that looks real. Use `<test-password>` in notes.

You already built a simple password checker in your first project, with length and has-number checks. That checker is a password tool in its simplest form. Extend it in lab the same small-step way: take a test password only, measure its length (under 12 is too short for a real password, in lab terms), check for lower case, upper case, digit, and symbol, count how many of those four types are present, and give a simple verdict — weak, medium, or strong — based on length first, then types. Don't save the test password, don't send it anywhere, and don't use it as a real password.

What the output means, with test examples only: an 8-character test word with a number, no upper case, and no symbol. A simple checker might call it medium; in real-life terms it is weak, because it is short and it is a common word plus a common number. A longer test phrase made of four unrelated words, with a number and a symbol, would be stronger — because of length and unpredictability, not because of the symbol alone.

Watch out for two misreadings: a checker saying "strong" does not mean the password is safe to reuse everywhere — strength is only one part, and reuse, storage, and guessing limits matter too. And never check a real password in an online checker or a public tool. That sends your secret to someone else. Check test passwords only, on your own machine only.

## Why common passwords fail

Guessing does not start randomly. It starts, in order, with common passwords, dictionary words, names, dates, and patterns, then combinations of those. That is why a common password fails fast, even if it looks clever to a human.

That matters for defence, not attack. If you understand that guessing starts with common lists, you understand why a system must require longer passwords, reject common passwords, limit guessing tries, and lock or slow down after repeated failures. Those are design rules.

For a lab app you built yourself, with test accounts only, that means three design rules: reject test passwords that are in a small common list you write yourself in lab (for example `password` and `123456` as examples of weak test words to reject), require a minimum length of 12 characters or more for a real password, and limit login tries while showing the same short error for any failure — "Username or password is incorrect." Don't say which part was wrong.

This file deliberately gives no list of real passwords to try, no method to guess a real account, and no tool to use against a real system. Those are not learning tasks. The learning task is understanding why common passwords fail, and designing a lab system that rejects them and limits tries. And remember: a password can be uncommon and still be short, predictable, or reused. Length, unpredictability, and not reusing matter together.

## Hashing and salting: how a good system stores passwords

A good system never stores a password in plain text. If it did, anyone who reads the storage file would read every password. Instead, it stores a hash.

A **hash** is a one-way fingerprint of a password. You can turn a password into a hash, but you cannot turn a hash back into the password, by design. When you log in, the system hashes what you typed and compares the two hashes. If they match, the password was correct. The system never needs to store the password itself.

A **salt** is a random value, different for each user, added before hashing. Salt means two users with the same password will have different hashes, and a pre-made list of hashes for common passwords won't work directly, because each hash is salted differently.

**Slow hashing for passwords** is a hashing method designed for passwords, deliberately slow, so guessing many passwords takes a long time. Fast hashes, designed for files, are wrong for passwords, because they let guessing go fast. In lab terms, remember: password hashing must be slow, salted, and one-way. The exact method name is a later, deeper topic — don't implement your own password storage from this file alone, and never store a real password in a lab file to "test" hashing.

The design, in outline: a user creates a password in lab, with test data only. The server generates a random salt, different for that user, hashes password plus salt with a slow password-hashing method, and stores only salt plus hash — never the password itself. Later, the user types a password, the server hashes it with the stored salt using the same method, and compares the two hashes in a way that doesn't leak timing. If they match, login succeeds. If not, the server shows the same short error and counts the failure for rate limiting.

If a lab app you built stores passwords in plain text, or in a file you might share, that is a design flaw to fix immediately, in lab, before you build anything else on top of it. Change the design to hash plus salt, or remove passwords from that lab app entirely and use test accounts with no real data. A lab habit becomes a real habit, so "it's only a lab" is not a reason to store plain text. And don't put a hash from a real system in a public note thinking it is safe because it is "only a hash" — a hash of a weak password can be guessed in lab by trying common words. Never publish a real hash.

## Password managers and multi-factor

A **password manager** creates long, random passwords for each site, stores them encrypted with one strong master password, and fills them in for you. It solves the reuse problem, because you no longer need to remember many passwords — only one strong master password, protected properly.

**Multi-factor authentication (MFA)** requires two or more different factors to log in — for example, something you know (password) plus something you have (a code from an app, or a security key). Even if a password leaks, an attacker would still need the second factor.

This part is not a lab task — it is a real-life habit that protects your real accounts, including the GitHub account you use for this roadmap. Use a password manager for real accounts, use long, random passwords per site, and turn on MFA where a site offers it, especially for email and GitHub. Each site needs its own password, and MFA where offered — MFA on one site does not protect another. Never share a master password or an MFA code in a note, screenshot, or chat. A code is a secret, for one use, for a short time. And a manager doesn't replace MFA: a manager helps with passwords, MFA adds a second, different factor.

One more thread that ties this file together: a system that allows unlimited guesses, even with strong passwords, is a design flaw. Limit tries, slow down failures, and log failures safely, without secrets in the log. Strength, reuse, storage, and guessing limits are separate design rules, and a good system gets all of them right.

Next: `system-tools.md`, then `safe-labs.md` — how password safety and system safety fit together in a lab.

---
*All examples here are for your own machine or your own private lab only, with test data only. Never publish a real password, real hash, real username, or real personal data in a note or public file.*
