--- 
title: John the Ripper - Cracking Hashes
date: 2025-10-31
categories: [Security]
tags: [johntheripper, hashes, passwords, security tools] #tags should always be lowercase
---

Throughout the tasks in this room, I will be using the following:

- The "Jumbo John" version of John the Ripper
- The RockYou password list

---

### Cracking Basic Hashes

The basic syntax of John the Ripper is as follows:

`john <options> <file-path>`

`options`: Specifies the options you want to use
`file-path`: Specifies the file containing the hash you're trying to crack

If you're unsure of what type of hash you're working with, you can run `john` in automatic cracking mode, which can be unreliable, but possible. The syntax to do this is as follows:

`john --wordlist=<path to wordlist> <filepath>`

If you want to use other tools to identify the hash, you can set `john` to a specific format. 

Some tools to identify hashes:

[hashes.com](https://hashes.com/en/tools/hash_identifier)

[Hash-Identifier Python](https://gitlab.com/kalilinux/packages/hash-identifier/-/tree/kali/master)

To use the hash-identifier python tool from the GitHub link above, we can use `wget` or `curl` to download the Python file `hash-id.py` and launch it with `python3 hash-id.py`. Then you can just enter the hash and it will give you a list of all the possible formats.

Once the hash has been identified, you can tell `john` to use it to crack the hash using the following syntax:

`john --format=<format> --wordlist=<path to wordlist> <filepath>`

---

>Note: When telling `john` to use standard hash type formats, you have to prefix it with `raw-` i.e., `--format=raw-md5`

---

**Question:**

>*What type of hash is hash1.txt?*

I simply ran the hash identifier python script and it came back with `MD5`. This was correct

**Question:**

>*What is the cracked value of hash1.txt?*

I entered the follow `john` command:

`john --format=raw-md5 --wordlist-/usr/share/wordlists/rockyou.txt hash1.txt`

And was returned with `biscuit` as the answer

**Question:**

>*What type of hash is hash2.txt?*

I used `cat` to read the hash, copied and pasted it into the python script again and came back with the answer: `SHA-1`, which was correct

**Question:**

>*What is the cracked value of hash2.txt?*

Running `john` again with the following syntax gave the correct answer, `kangaroo`:

`john --format=raw-SHA1 --wordlist=/usr/share/wordlists/rockyou.txt hash2.txt`

**Question:**

>*What is type of hash is hash4.txt?*

Checked it in the Python hash identifier script and it came back with two possible hashes and two least possible hashes: `sha512` and `whirlpool` for both of them. So I tried `whirlpool` and it was correct.

**Question:**

>*What is the cracked value of hash4.txt?*

This time I ran the `john` command without the `raw-` option because I felt like `whirlpool` wasn't a standard hash (I ended up being correct)

`john --format=whirlpool --wordlist=/usr/share/wordlists/rockyou.txt hash4.txt`

The value was: `colossal`

---

### Cracking Windows Authentication Hashes:

Authentication hashes are the hashed versions of passwords stored by operating systems, sometimes possible to crack using brute-force methods. To get your hands on these hashes, you must often already be a privileged user.

`NTHASH`, or `NTLM` (as it's commonly referred to as) is the hash format modern windows OS machines use to store user and service passwords.

In Windows, *SAM* (Security Account Manager) is used to store user account information, including usernames and hashed passwords. You can acquire these hashes by dumping the SAM database on a Windows machine, using a tool like *Mimikatz*, or using the Active Directory database `NTDS.dit`.

**Question:**

>*What do we need to set the `--format=` flag to in order to crack the hash?*

This one was kind of tricky to figure out. It was hinted at in the lesson but they also didn't outright say what it was. I started with the Python script to try and identify the hash. But it returned `md5` and when I tried that format with `john`, it didn't work. 

Then I tried googling it (Lol) and the first thing that came up was that it used `md4`, tried that with `john`, unsuccessful. So I went back to the lesson and saw that NTHash/NTLM both shared "NT" in the name, and that the "NT" line became the standard OS type to be released by Microsoft. So I tried that, and it was correct. `NT` was the answer.

**Question:**

>*What is the cracked value of this password?*

With the hash type solved, I entered it into `john`, without the `-raw-` flag, and I cracked the password: `mushroom`

---

### Cracking Hashes from /etc/shadow

The `etc/shadow` file is the file on Linux machines where password hashes are stored. It contains one entry per line for each user or user account of the system. This file is usually only accessible as root. 

In order for `john` to crack these hashes, you must combine `/etc/shadow` with `/etc/passwd` for `john` to understand the data it's being given using a process called *Unshadowing*. 

To do this, we can use a built in tool in `john` called `unshadow`. The basic syntax is as follows:

`unshadow <path to passwd> <path to shadow>`

`unshadow`: invokes the unshadow tool
`<path to passwd>`: The file that contains the copy of the `/etc/passwd` file you've taken from the target machine]
`<path to shadow>`: The file that contains the copy of the `/etc/shadow` file you've taken from the target machine

>Example: `unshadow local_passwd local_shadow > unshadowed.txt`

When using `unshadow`, you can either use the entire `/etc/passwd` and `/etc/shadow` files, assuming you have them available, or you can use the relevant line from each, for example:

>FILE 1-local_passwd

Contains the `/etc/passwd` line for the root user:

`root:x:0:0::/root:/bin/bash`

>FILE 2-local_shadow

Contains the `/etc/shadow` line for the root user

We can then feed the output from `unshadow` txt file directly into `john`. Normally, you would not need to specify a hash type here as we have made the input specifically for `john`, however, in some cases, you will need to specify the format as we have done previously.

**Question:**

>*What is the root password?:*

First I ran the `unshadow` command:

`unshadow local_passwd local_shadow > unshadowed.txt`, just like the example

`cat unshadowed.txt` to be sure I got an output that made sense

`john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt` note I didn't use the `--format` flag here, and I didn't end up needing to

`john` cracked the hash: `root` was the answer

---

### Single Crack Mode

`john`has a mode called the *Single Crack mode*. In this mode, `john` only uses the information provided in the usernames to try and work out possible passwords by slightly changing the letters and numbers contained within the username through a technique called, *"Word Mangling"*

Looking at the hash entries for both `/etc/shadow` and `/etc/passwd`, you'll see the fields are separated by colons `:`. The fifth field in the user account record is the *GECOS* field (General Electric Comprehensive Operating System). It stores general information about the user, such as the users full name, office number, telephone number, etc. `john` can take information stored in those records, and add it to the wordlist it generates when cracking `/etc/shadow` hashes with single crack mode.

To use single crack mode, we use a similar syntax as used previously.

`john --single --format=<format> <filepath>`

When cracking hashes in single crack mode, you need to change the file format that you're feeding `john` for it to understand what data to create a wordlist from. You do this by prepending the hash with the username that the hash belongs to. For example, we could change the hash in `hashes.txt`:

From: `<hashstring>`
To: `username:<hashstring>`

Where `username` is the username you're targeting and `<hashstring>` is the string of characters that make up the hash.

**Question:**

>*What is Joker's password?:*

So the first thing I did here was nano into the `hash07.txt` file and add `joker:` at the very beginning of the hash.

I then tried to run `john` with no format:

`john --single hash07.txt`

But I was getting multiple errors as `john` was unable to figure out the hash type on it's own. So I copied the hash without the `joker:` part of it, and entered it into a hash type identifier ([hashes.com](https://hashes.com/en/decrypt/hash)), and found that it was `md5`

Tried `john` again, this time including the hashtype:

`john --single --format=raw-md5 hash07.txt`

And it was able to crack the hash: `Jok3r` was the answer

---

### Custom Rules

When using Single Crack Mode, you can define a set of rules for `john` to create passwords with. This is beneficial when you know information about the password structure of whatever your target is.

Many organizations will require a certain level of password complexity to try and combat dictionary attacks. Password complexity is good, however we can exploit the *human factor* here. Many users will use predictable locations for the password complexity requirements like:

- Lowercase Letter
- Uppercase letter
- Number
- Symbol

For example, given those requirements, many users would structure similar to the following:

`Password12!`
`P@ssword!`

etc, etc.

Custom rules are defined in the `john.conf` file. This file can be found in `/etc/john/john.conf` usually.

You can refer to the docs for the syntax to create rules:

[`john` Custom Rules Docs](https://www.openwall.com/john/doc/RULES.shtml)

To call up a rule you've created, you can use the flag `--rule=<rulename>`

i.e. `john --wordlist=<wordlistpath> --rule=<rulename> <filepath>`

---

### Cracking Password Protected Zip Files

We can use `john` to crack the password on password-protected zip flies by using a separate part of the `john` tools suite to convert the zip file into a format that `john` will understand. 

Similar to the `unshadow` tool, we can use `zip2john` to convert the zip file into a hash format that `john` can understand and hopefully crack. Basic syntax is as follows:

`zip2john [options] [zipfile] > [output file]`

- `[options]`: Allows you to pass specific checksum options to `zip2john`, however, this often isn't necessary

>Example: `zip2john zipfile.zip > zip_hash.txt`

We can then take the file we output from `zip2john`, and feed it directly into `john`.

**Question:**

>*What is the password for the secure.zip file?*

Following the above instructions, I simply ran the following to convert the zip file into a readable format for `john`:

`zip2john secure.zip > zip_hash.txt`

Then,

`john --wordlist=/usr/share/wordlists/rockyou.txt zip_hash.txt`

The cracked hash was `pass123`

**Question:**

>*What is the contents of the flag inside the zip file?*

After cracking the password for the zip file, it was time to unzip it, and explore the folder's contents.

`unzip secure.zip`

Extracted a directory called `zippy`

In that directory, there was a `flag.txt` file where the answer to this question was found:

`THM{w3ll_d0n3_h4sh_r0y4l}`

---

### Cracking a Password-Protected RAR Archive

Almost identical to using `zip2john`, we can use the `rar2john` tool to convert the RAR file into a hash format that `john` can understand:

`rar2john [rar file] > [output file]`

**Question:**

>*What is the password for the secure.rar file?*

Using:

`rar2john secure.rar > rar.txt`

And then:

`john --wordlist=/usr/share/wordlists/rockyou.txt rar.txt`

I discovered the password to the RAR file was: `password` Lol, **security!**

**Question:**

>*What are the contents of the flag inside the zip file?*

Using `unrar` I unzipped the RAR file using the password cracked with `john`

`unrar x secure.rar`

It exposed a `flag.txt` file which I read with `cat` giving me the answer:

`THM{r4r_4rch1ve5_th15_t1m3}`

---

### Cracking SSH Key Passwords

`john` can also crack the SSH private key passwords of `id_rsa` files. Unless configured otherwise, you authenticate your SSH login using a password. However, you can configure key-based authentication, which lets you use your private key, `id_key`, as an authentication key to log in to a remote machine over SSH. Doing so will often require a password to access the private key. We will be using `john` to crack this password to allow authentication over SSH using the key.

Normally you can just run it using a similar syntax to the previous conversion tools, as long as it's installed:

`ssh2john [id_rsa private key file] > [output file]`

**Question:**

>*What is the SSH private key password?*

In this case, I used:

`python3 /opt/john/ssh2john.py id_rsa > rsa.txt`

Because `ssh2john` isn't installed on this attackbox. With it installed, I could've run:

`ssh2john id_rsa > rsa.txt`

And then took that .txt file and fed it to `john`:

`john --wordlist=/usr/share/wordlists/rockyou.txt rsa.txt`

Which gave me the SSH private key password: `mango` which was the correct answer.

---

### Conclusion

This room was extremely fun. 