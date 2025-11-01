--- 
title: TCPDump Basics
date: 2025-10-22
categories: [Security]
tags: [tcpdump, logging, networking, security tools] #tags should always be lowercase
---

Today I went through the TCPDump room. The goal of the room is to learn how to use `tcpdump` to:

- Capture packets and save them to a file
- Set filters on captured packets
- Control how captured packets are displayed

---

### Basic Packet Capture

``tcpdump`` can be run without any arguments to check if it is installed. But to actually get a use out of it, you first need to specify which network interface to listen to.

``ip address show`` will list the network interface devices available

From there you can run ``tcpdump -i <interface>`` (May have to run as sudo) and ``tcpdump`` will start to capture packets.

You can save captured packets with the ``-w <filename>`` command. You can also use ``tcpdump`` to read packets from a file with the ``-r <filename>`` command.

You can specify the number of packets captured by using the ``-c <packetscount>`` command, otherwise ``tcpdump`` will continue to capture packets until interrupted.

``tcpdump`` will resolve IP addresses and show friendly domain names where it can. To avoid this, you can use the ``-n`` argument. If you also don't want port numbers to be resolved to their respective service, you can use ``-nn`` to stop both DNS and port number lookups.

If you want to see more details about the packets, you can use the ``-v`` argument to produce a slightly more detailed output. There is also ``-vv`` and ``-vvv`` which produce more details respectively. Play around with this to see the types of details each version of ``-v`` produces.

---

### Filtering

``tcpdump`` is capable of filtering. Filtering by host, filtering by port and filtering by protocol.

Examples:

``tcpdump host example.com -w http.pcap`` - Captures all the packets exchanged with example.com and saves them to a ``.pcap`` file called ``http.pcap``. If you want to limit those packets to a specific source or destination you could use ``dst host <ip>`` or ``dst host <hostname>`` for destination and ``src host <ip>`` or ``src host <hostname>`` for the source.

``tcpdump -i ens5 port 53 -n`` - Captures all the packets on interface ``ens5`` on port 53 without resolving IP's to domain names. Same as with host filtering, you can specify source and destinations with ``src port <portnumber>`` and ``dst port <portnumber>``

Lastly, we can filter by protocol with the following ``tcpdump -i ens5 <protocol>`` where <protocol> could be something like ``icmp`` or ``http``

There are also some logical operators that could be handy

- ``and`` - captures packets where both conditions are true, i.e. ``tcpdump host 1.1.1.1 and tcp`` captures ``tcp`` traffic with host `1.1.1.1`
- ``or`` - captures packets where either one of the conditions is true, i.e. ``tcpdump udp or icmp`` captures `udp` or `icmp` traffic
- ``not`` - captures packets where the conditions are not true, i.e. ``tcpdump not tcp`` captures all packets except for `tcp`

---

### Displaying Packets

``tcodump`` offers many options to display/print packets.

- ``-q`` - Quick output; print brief packet info
- ``-e`` - Prints link-level header
- ``-A`` - Shows packet data in ASCII
- ``-xx`` - Shows packet data in hexadecimal format
- ``-X`` - Shows packet headers and data in hex and ASCII


