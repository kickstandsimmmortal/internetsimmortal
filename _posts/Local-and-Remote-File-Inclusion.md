--- 
title: File Inclusion and Directory Traversal Vulnerabilities
date: 2025-07-23
categories: [bug bounty]
tags: [bug bounty, hacking, fundamentals] #tags should always be lowercase
---

**Local File Inclusion (LFI)**

**Remote File Inclusion (RFI)**

**Directory Traversal**

Web Applications can be written to accept requests for specific files on a system via parameters with PHP. That's what we'll target

![Parameters Image](/assets/img/parameters.png)

### **Directory Traversal**

The following image shows an example of the concept

![DirectoryTraversal](/assets/img/directory_traversal.png)

These attacks take advantage of moving the directory one step up using the ``../`` if you can find an entry point (like, ``get.php?file=``). Can also be effective on Windows servers. It takes advantage of the ``get_file_contents`` feature in PHP.

I'd want to be looking for some of these common OS files that could be interesting to stumble onto:
- ``/etc/issues`` - could contain a message or some sort of identification to be shown before login prompt
- ``/etc/profiles`` - controls system-wide default variables
- ``/proc/version`` - specifies linux kernel version
- ``/etc/passwd`` - all registered users that have access to a system
- ``/etc/shadow`` - contains information on users' passwords
- ``/root/.bash_history`` - contains history of ``root`` user's commands
- ``/var/log/dmessage`` - global system messages, like startup logs
- ``/var/mail/root`` - all emails for ``root`` user
- ``/root/.ssh/id_rsa`` - SSH keys for root or known valid user on server
- ``/var/log/apache2/access.log`` - accessed requests for ``Apache`` web server

### **Local File Inclusion (LFI)**

Focusing on PHP here. But this kind of vulnerability could also appear in languages like ASP, JSP, or Node.js. 

Takes advantage of PHP functions such as ``include``,``require``,``include_once``, and ``require_once``

``/etc/passwd`` is the exploit

``include`` may sometimes contain a directory within the function:

```
    <?PHP
        include("interestingfolder/". $_GET['lang']);
    ?>
```
Which could potentially be a target. Let's do a small example. Let's say we're visiting ``www.yadaya.com/page1.php``. I'd just add the exploit here that could potentially show me some user information on the system:

``www.yadayada.com/page1.php?file=/etc/passwd``

And to find an ``include`` function that could potentially show me a directory I could get to, I would just click around the site, try to mess with the URL to force errors, and look for information leaks that could expose a folder.

If we are unable to view source code, we look deeper at the errors to understand how data is processed into the web  app.

For example, if you had a valid entry point like ``http://yadayada.com/index.php?lang=EN`` you could throw an invalid input in the URL. Like ``http:yadada.com/rip.php?lang=EN`` you would get an error that could expose directories, and from there you can ``../`` exploit. Look for the ``include`` function. It will still read the input with a ``.php`` at the end. 

Use **Null Byte**, which is %00 (only works with earlier versions than PHP 5.3.4)

If the devloper is using input validation, you can use ``....//....//....//....//etc/passwd``. This works because the PHP filter only replaces the first subset string ``../`` and doesn't do another pass. So it reads as normal.

