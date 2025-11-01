--- 
title: Hash Cracking with HashCat
date: 2025-10-30
categories: [Security]
tags: [hashcat, hashes, passwords, security tools] #tags should always be lowercase
---

*Hashing* is used for securing passwords in authentication systems.

I was given some hashes to crack on TryHackMe, so I thought I'd document how I cracked them here

**Question:** 

>*Use hashcat to crack the hash, $2a$06$7yoU3Ng8dHTXphAg913cyO6Bjs3K5lBnwq5FJyA6d01pMSrddr1ZG, saved in ~/Hashing-Basics/Task-6/hash1.txt:*

The first thing I did was refer to the `hashcat` wiki @ https://hashcat.net/wiki/doku.php?id=example_hashes to identify the hash mode. The prefix `"$2a$"` is a hash indentifier so I searched for it on that page and found it at hash mode `3200`, `bcrypt`.

I then took that information and gave it to `hashcat` with the command

`hashcat -m 3200 -a 0 hash1.txt /usr/share/wordlists/rockyou.txt`

`-m` is the hash-mode in numeric format to use

`-a 0` is the attack mode, in this case, `0` is for straight, i.e., trying one password from the wordlist after the other

When it finished the result wasn't immediately obvious to me, but after the original hash, there was a colon with the following numbers: `85208520`

Which ended up being the correct answer.

![img-description](/assets/img/hash1result.png)

**Question:**

>*Use hashcat to crack the SHA2-256 hash, 9eb7ee7f551d2f0ac684981bd1f1e2fa4a37590199636753efe614d4db30e8e1, saved in saved in ~/Hashing-Basics/Task-6/hash2.txt*

Same thing as before, I referred to the `hashcat` wiki, searched for `SHA2-256` and found it's hash-mode as `1400`.

`hashcat -m 1400 -a 0 hash2.txt /usr/share/wordlists/rockyou.txt`

Hashcat returned the value as: `Halloween`, which was the correct answer

![img-description](/assets/img/hash2result.png)

**Question:**

>*Use hashcat to crack the hash, $6$GQXVvW4EuM$ehD6jWiMsfNorxy5SINsgdlxmAEl3.yif0/c3NqzGLa0P.S7KRDYjycw5bnYkF5ZtB8wQy8KnskuWQS3Yr1wQ0, saved in ~/Hashing-Basics/Task-6/hash3.txt:*

Following the same exact steps as before, I searched for `$6$` on the wiki, found it's hash-mode at `1800`, `sha512crypt`, and entered the following command:

`hashcat -m 1800 -a 0 hash3.txt /usr/share/wordlists/rockyou.txt`

Which returned the value: `spaceman`

![img-description](/assets/img/hash3result.png)

**Question:**

>*Crack the hash, b6b0d451bbf6fed658659a9e7e5598fe, saved in ~/Hashing-Basics/Task-6/hash4.txt*

With no prefix to go off here, I was unsure if I could use `hashcat` to crack this one. So I went to [crackstation.net](https://crackstation.net/) and entered the hash there. It returned the value `funforyou` as hash-type `md5`. This was correct!

![img-description](/assets/img/hash4result.png)

That concludes my very first time cracking hashes!

