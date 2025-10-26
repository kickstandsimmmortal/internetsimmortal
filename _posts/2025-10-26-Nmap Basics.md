--- 
title: Nmap - The Basics
date: 2025-10-26
categories: [Security]
tags: [nmap, networking scanning, security tools] #tags should always be lowercase
---


`Nmap` can be used to discover live hosts on a network. There are a few ways to specify its targets:

- IP range using `-`  : if you want to scan all IP addresses from 192.168.0.1 to 192.168.0.10, you could use `192.168.0.1-10`

- IP Subnet using `/` : If you want to scan a subnet, you can express it as `192.168.0.1/24` which would be the equivalent of scanning `192.168.0.0-255`

- Hostname: You can also specify targets by hostname, i.e. `example.com`

If you want to discover online hosts on a network, you can use the `-sn` option (ping scan). `-sn` aims to discover live hosts without attempting to discover the services running on them. Ideal if you want to discover devices on a network without causing too much noise.

Normally, you would run `nmap` as `root` or using `sudo` so as not to restrict nmaps abilities with account privileges.

When scanning a local network, `nmap` starts by sending ARP requests. When scanning a remote network, ARP requests are unavailable. When scanning remote networks, `nmap` uses ICMP (ping) requests.

`nmap` also offers a list scan with the option `-sL` which only lists targets to scan, without actually scanning them. This option helps confirm targets before running the actual scan.

**Question:** 

>*What is the last IP address that will be scanned when your scan target is `192.168.0.1/27`?*

I tried `nmap -sL 192.168.0.1/27` and entered the last IP that was in the list. This was the correct answer: `192.168.0.31`

---

### Scanning TCP ports for running services

#### Connect Scan

`nmap`'s connect scan will attempt to complete the TCP three-way handshake with every target port. If the TCP port turns out to be open and Nmap connects successfully, `nmap` will tear down the established connection. 

The connect scan can be triggered using `-sT`

#### SYN Scan (Stealth)

Unlike the connect scan, the SYN scan only executes the first step of the TCP three-way handshake, by sending a TCP SYN packet. The three-way handshake is never completed. This is expected to lead to fewer logs as the connection is never established, so it is considered a relatively stealthy scan. 

Triggered by using the `-sS` flag.

### Scanning UDP ports for running services

Most services use TCP for communication, but many also use UDP (i.e. DNS, DHCP, NTP, SNMP, VoIP). UDP does not require establishing a connection and tearing it down afterwards, and it is very suitable for real-time communication, such as live broadcasts.

`nmap` offers the option `-sU` to scan for UDP services. The most common 1,000 ports are scanned by default. However, there are a few options if this isn't what you're looking for:

- `-F` "Fast Mode", scans the 100 most common ports rather than the default 1,000

- `-p[range]` allows you to specify ports to scan (i.e. `-p10-1024` scans from port 10 to 1024) 

- `-p-` scans all ports and is the most thorough option

**Questions:**

>*How many TCP ports are open on the target system at 10.201.18.115?*

I simply ran `nmap -sT 10.201.18.115` and discovered 6 open TCP ports on the host. 6 was the correct answer.

>*Find the listening web server on `10.201.18.115` and access it with your browser. What is the flag that appears on its main page?*

This was also pretty simple, using the results from the last questions scan, there was an `http` service running on port `8008`. So I just entered the IP and port into my web browser and the page with the flag was displayed. `10.201.18.115:8008`

---

### OS Detection

You can enable OS detection with the `-O` option added to your command. This isn't always 100% accurate and should be viewed more as an educated guess.

i.e. `nmap -sS -O 192.168.124.211`

### Service and Version Detection

The `-sV` flag enables version detection.

i.e. `nmap -sS -sV 192.168.124.211`

If you wanted both `-O`, `-sV` and more options in one, you could use `-A`. This option enables OS detection, version scanning, traceroute and more in one flag.

`-Pn` flag enables forced scanning hosts that appear to be down.

**Question:** 

>*What is the name and detected version of the web server running on `10.201.18.115`?*

I ran the command `nmap -sS -sV 10.201.18.115` which revealed the same 6 services/ports that were listed earlier, however this time with versions of the services listed alongside them. The web server is running `lighttpd 1.4.74`

---

### Scan Speeds

Running scans at normal speed might trigger IDS or other security solutions, therefore it is reasonable to control how fast a scan should go. `nmap` offers timing templates:

- `-T0` : Paranoid, 9.8 hours

- `-T1` : Sneaky, 27.53 minutes

- `-T2` : Polite, 40.56 seconds

- `-T3` : Normal, 0.15 seconds

- `-T4` : Aggressive, 0.13 seconds

A second helpful option is the number of parallel service probes. This can be controlled with `--min-parallelism <numprobes>` and `--max-parallelism <numprobes>`. These options are used to set a minimum and maximum on the number of TCP and UDP port probes active simultaneously for a host group.

`nmap` will automatically control the number of parallel probes by default.

A similar helpful option is `--min-rate <number>` and `--max-rate <number>`. They control the rates at which nmap sends packets. The rate is provided as the number of packets per second. It applies to the entire scan, not to a single host.

Lastly, `--host-timeout <time>` specifies the maximum time you are willing to wait.

**Question:**

>*What is the non-numeric equivalent of `-T4`?*

`-T aggressive`

---

### Controlling Nmap's Output

If you want real-time information about the scan progress, you can enable verbose output with `-v`. You can increase the verbosity level by adding another "v" to the flag, such as `-vv` or even `-vvvv`. You can even increase the verbosity level by pressing "v" after the scan already started. You can also specify the verbosity level directly, by using `-v2` or `-v4` for example.

If the verbosity options don't suit your needs, consider using the `-d` for debugging-level output. Same as verbosity, this can be specified directly, `-d2` or the max, `-d9`.

Saving scan reports is essential, and ``nmap`` gives us various options to do so:

- `-oN <filename>`: Normal output

- `-oX <filename>`: XML output

- `-oG <filename>`: `grep`-able output

- `-oA <basename>`: Output in all major formats

That concludes the Nmap basics room on TryHackMe
