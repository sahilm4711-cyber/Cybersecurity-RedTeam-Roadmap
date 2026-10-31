# 06 — Password and System: Password Tools, Fully Explained

## What this file is

This is Phase 5, part 1. You already know Linux, networks, and the web. Now you learn password tools — not to break other people's passwords, but to understand, in simple words, what makes a password strong, how a good system stores it, and how to check your own test passwords safely.

If you can explain, in simple English, length, character types, entropy in simple terms, hashing, and salting, and you can check a test password without ever using a real one, you understand password tools. If you only know a tool name, you do not.

No shortcuts here. Each concept gets: simple explanation, what, why, when, when NOT to use it, lab example with test data only, output meaning, and common mistakes.

**Safety rule for this whole file — read this first:**
- Use test passwords only, like `hello123` as a learning example in this file, or a test word you invent for lab, never a real password you use anywhere else.
- Never test, check, crack, or try to guess a real person's password, a real account, or a password file you do not own.
- Never put a real password, a real hash from a real system, or a real username in a note, screenshot, or public file. Use placeholders: `<test-password>`, `test-user`, `your-user`.
- Password tools in this file are for learning and for checking your own lab design. They are not tools to use against anyone else.

---

## 1. What is a password, in simple words?

**Simple explanation:**
A password is a secret you know, that a system checks to confirm you are who you claim to be. It is one factor of authentication: something you know. Other factors are something you have (a phone, a key) and something you are (a fingerprint) — those are later topics, not this file.

**Practical design note:**
A password system has three parts, and a flaw in any part breaks the whole system:
1. Creation: How does a user choose a password? Is it long enough? Is it checked for weakness?
2. Storage: How does the server store it? In plain text (never), or as a hash (correct, see Section 4)?
3. Checking: How does the server compare what you typed with what it stored, without leaking which part was wrong, and without allowing unlimited guesses?

A red-team learner, later, checks all three parts in a lab app they built themselves — not in a real app.

---

## 2. What makes a password strong? Length, types, and guessing time

**Simple explanation, in order of importance:**
1. **Length matters most.** A longer password takes much longer to guess, even if it is simple words. A 16-character password made of four random words is, in simple terms, much stronger than an 8-character password with symbols, because the guessing space grows with every character.
2. **Unpredictability matters next.** A password that is a common word, a name, a date, or a pattern like `123456` is easy to guess, because attackers in a lab try common lists first. A password that is random, or made of unrelated words you chose, is harder to guess.
3. **Character types help, but do not fix a short password.** Using upper case, lower case, numbers, and symbols increases the guessing space, but only a little compared to adding length. A short password with symbols is still short.

**A simple way to think about guessing time, without maths:**
Imagine a lock with a combination. Each extra character is like adding another wheel to the lock. Each extra wheel multiplies the number of combinations. That is why length grows strength so fast, and why a short password, no matter how clever, is still a small lock.

**What NOT to do, in lab and in real life:**
- Do not use the same password for many sites. If one site leaks it, all sites using it are at risk.
- Do not use a password that is your name, a pet name, a birthday, or a common word with a number at the end, like a test example `hello123` in real life. In this file, `hello123` is only a learning example to show weakness, not a password to use.
- Do not share a password in a note, screenshot, chat, or public file, even a test one that looks real. Use `<test-password>` in notes.

**Lab example — checking a test password, own machine only:**
You already built a simple password checker in your first project, with length and has-number checks. That checker is a password tool, in its simplest form. Extend it, in lab, with the same small-step method you used there:

```
Algorithm: CHECK_TEST_PASSWORD_STRENGTH
Step 1: Input a test password only, never a real one.
Step 2: Measure length. If length < 12, note "too short for a real password, in lab terms".
Step 3: Check has_lower: is there a lower-case letter?
Step 4: Check has_upper: is there an upper-case letter?
Step 5: Check has_digit: is there a number?
Step 6: Check has_symbol: is there a symbol like ! @ # ?
Step 7: Count how many of those four types are present.
Step 8: Give a simple verdict, in lab terms: weak, medium, or strong, based on length first, then types.
Step 9: Do NOT save the test password, do NOT send it anywhere, and do NOT use it as a real password.
```

**Output meaning, test examples only:**
- `hello123` -> length 8, medium in a simple checker, has number True, has upper False, has symbol False. In real-life terms, this is weak, because it is short and it is a common word plus a common number. In this file, it is only an example of a weak test word.
- A longer test phrase made of four unrelated words, with a number and a symbol, in lab terms, would be stronger, because of length and unpredictability, not because of the symbol alone.

**Common mistakes:**
- Thinking a password checker that says "strong" means the password is safe to reuse everywhere. Strength is only one part; reuse, storage, and guessing limits matter too.
- Thinking symbols alone make a password strong. Length first, then unpredictability, then types.
- Checking a real password in an online checker, or in a public tool. That sends your secret to someone else. Check test passwords only, on your own machine only.

**Practice task:**
On your own machine only, with test words only, run your password checker (or the algorithm above, on paper) for three test words: one short common word, one longer phrase of unrelated words, and one with upper, number, and symbol. Write, for each, length, types present, and a simple verdict, in your own words. Do not write any real password down.

---

## 3. Password lists and guessing, concept only — why common passwords fail

**Simple explanation:**
Attackers in a lab do not guess randomly first. They try, in order: common passwords, dictionary words, names, dates, and patterns, then combinations of those. That is why a common password fails fast, even if it looks clever to a human.

**Why this matters for defence, not attack:**
If you understand that guessing starts with common lists, you understand why a system must: require longer passwords, reject common passwords, limit guessing tries, and lock or slow down after repeated failures. Those are design rules, not attack instructions.

**Lab-only defence idea:**
For a lab app you built yourself, with test accounts only:
1. Reject test passwords that are in a small common list you write yourself in lab (for example, a list with `password`, `123456`, and `hello123` as examples of weak test words to reject).
2. Require a minimum length, in lab terms, of 12 characters or more for a real password, and explain why in your lab notes.
3. Limit login tries in lab, and show the same short error for any failure: "Username or password is incorrect." Do not say which part was wrong.

**What this file does NOT give:**
This file does not give a list of real passwords to try, a method to guess a real account, or a tool to use against a real system. Those are not learning tasks. The learning task is: understand why common passwords fail, and design a lab system that rejects them and limits tries.

**Common mistake:**
Thinking "my password is not on a common list, so it is safe". A password can be uncommon and still be short, predictable, or reused. Length, unpredictability, and not reusing matter together.

---

## 4. Hashing and salting — how a good system stores passwords

**Simple explanation:**
A good system never stores a password in plain text. If it did, anyone who reads the storage file would read every password. Instead, it stores a hash.

- **Hash** = a one-way fingerprint of a password. You can turn a password into a hash, but you cannot turn a hash back into the password, by design. When you log in, the system hashes what you typed, and compares the two hashes. If they match, the password was correct. The system never needs to store the password itself.
- **Salt** = a random value, different for each user, added before hashing. Salt means two users with the same password will have different hashes, and a pre-made list of hashes for common passwords will not work directly, because each hash is salted differently.
- **Slow hashing for passwords** = a hashing method designed for passwords, that is deliberately slow, so guessing many passwords takes a long time. Fast hashes, designed for files, are wrong for passwords, because they let guessing go fast. In lab terms, remember: password hashing must be slow, salted, and one-way. The exact method name is a later, deeper topic — do not implement your own password storage from this file alone, and never store a real password in a lab file to "test" hashing.

**Algorithm: STORE_AND_CHECK_A_PASSWORD, concept only, lab design**

```
Step 1: User creates a password, in lab, with test data only.
Step 2: Server generates a random salt, different for that user.
Step 3: Server hashes password + salt, with a slow password-hashing method.
Step 4: Server stores only salt + hash, never the password itself.
Step 5: User logs in later, types a password.
Step 6: Server hashes typed password + stored salt, with the same method.
Step 7: Server compares the two hashes, in a safe way that does not leak timing.
Step 8: If they match, login succeeds. If not, show the same short error, and count the failure for rate limiting.
```

**Practical design note:**
If a lab app you built stores passwords in plain text, or in a file you might share, that is a design flaw to fix immediately, in lab, before you build anything else on top of it. Change the design to hash + salt, or remove passwords from that lab app entirely and use test accounts with no real data.

**Common mistakes:**
- Storing a password in plain text "because it is only a lab". A lab habit becomes a real habit. Store hashes, or store nothing.
- Using a fast hash, or no salt, and thinking "we hash, so we are safe". Hashing without salt and without slowness is weak for passwords.
- Putting a hash from a real system in a public note, and thinking it is safe because it is "only a hash". A hash of a weak password can be guessed in lab by trying common words. Never publish a real hash.

**Practice task:**
Write, in your own words, without code, the STORE_AND_CHECK_A_PASSWORD algorithm above, from memory. Then explain, in one sentence each, what a hash is, what a salt is, and why password hashing must be slow.

---

## 5. Password managers and multi-factor, simply

**Simple explanation:**
- **Password manager** = a tool that creates long, random passwords for each site, stores them encrypted with one strong master password, and fills them in for you. It solves the reuse problem, because you no longer need to remember many passwords, only one strong master password, protected properly.
- **Multi-factor authentication (MFA)** = requiring two or more different factors to log in, for example: something you know (password) + something you have (a code from an app, or a security key). Even if a password leaks, an attacker in lab terms would still need the second factor.

**Practical design note, for your own accounts:**
Use a password manager for real accounts, use long, random passwords per site, and turn on MFA where a site offers it, especially for email and GitHub. That is not a lab task — it is a real-life habit that protects your real accounts, including the GitHub account you use for this roadmap.

**Common mistakes:**
- Using one password for email and GitHub, and thinking MFA on one site protects the other. Each site needs its own password, and MFA where offered.
- Sharing a master password, or a MFA code, in a note, screenshot, or chat. A code is a secret, for one use, for a short time. Never share it, and never publish it in a public file.
- Thinking a password manager means you do not need MFA. A manager helps with passwords; MFA adds a second, different factor.

---

## 6. Common mistakes across password tools

- Checking, testing, or trying to guess a real password, a real account, or a real password file. Lab test data only, always.
- Storing a password, a hash from a real system, or a session value in a public note, screenshot, or repo. Use placeholders.
- Thinking a "strong" verdict from a checker means a password is safe to reuse, or safe to store in plain text. Strength, reuse, storage, and guessing limits are separate design rules.
- Building your own password storage, or your own encryption, from a simple file alone, and using it for real data. Password storage is a solved problem with proper methods — learn the concept here, and use a proper, reviewed method later, with guidance, before any real use.
- Forgetting rate limiting: a system that allows unlimited guesses, even with strong passwords, is a design flaw. Limit tries, slow down failures, and log failures safely, without secrets in the log.

---

## 7. Practice tasks — lab only, test data only

1. Run your password checker, or the algorithm in Section 2, on three test words only. Write length, types, and verdict for each, in your own words.
2. Write, from memory, the STORE_AND_CHECK_A_PASSWORD algorithm, and explain hash, salt, and slowness in one sentence each.
3. For a lab app you built yourself, write three design rules: minimum length, reject common test words, and limit login tries with a short, safe error message.
4. Check your own real accounts (not in a public note): Do you reuse any password across sites? Do you have MFA on email and GitHub? Write a private, personal action list, with no passwords in it, for what you will change first.
5. Read `system-tools.md` next, then `safe-labs.md`. For each, write one sentence about how password safety and system safety fit together in a lab.

---

## 8. Checklist — you are ready to move on when you can do all of these

- [ ] I can explain, in simple words, what a password is, and what the three parts of a password system are.
- [ ] I can explain why length matters most, then unpredictability, then character types.
- [ ] I can check a test password with length and type checks, and give a simple verdict, without using a real password.
- [ ] I can explain why common passwords fail fast, and what design rules stop that in a lab app.
- [ ] I can explain hash, salt, and slow password hashing, in simple words, and why plain-text storage is never acceptable.
- [ ] I can explain password managers and MFA simply, and why they matter for real accounts.
- [ ] I have not checked, guessed, or tried to use a real password, account, or password file.
- [ ] My notes contain no real password, real hash, real username, or real personal data — only test data and placeholders.

**Next file:**
After this, read `system-tools.md` in the same folder. It takes the same lab-only, own-machine-only method and applies it to checking your own system safely.

---
*All examples in this file are for your own machine or your own private lab only, with test data only. Never publish a real password, real hash, real username, or real personal data in a note or public file.*
