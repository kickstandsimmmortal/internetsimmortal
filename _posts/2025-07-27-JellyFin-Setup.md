--- 
title: Selfhosted Streaming with Jellyfin + Ubuntu Server
date: 2025-07-27
categories: [selfhosting]
tags: [selfhosting, streaming, linux] #tags should always be lowercase
---

This is the first official self-hosted anything I've ever done. I have a couple more plans in the future but for now I wanted what is essentially my own Netflix, I called it "Kickflix" - Lol.

I had two old Optiplex 7020 desktop computers laying around so I decided to use one of them to do this. At the moment it only has one 500GB HDD in it so I will definitely be needing some more storage soon in order to have a complete, and high quality library, but for now-this will do.

I decided to install Arch Linux on the desktop and run an Ubuntu server in a VM on the Arch Host instead of just installing Ubuntu server directly on the hardware. I could've just done that and it probably would've been easier but I wanted to play around more with virtualization tech.

There were a couple of quirks when trying to get the VM to start though. The first error I got when trying to spin up the VM was:

``Kernel driver not installed (rc=-1908)``

I found this command in the Arch forums that worked for me:

``sudo modprobe vboxdrv``

My understanding of this command is that it loads the VirtualBox kernel module, which is necessary to run a VM. I believe I may have to run this command everytime I reboot the Host. Although, I'm sure there's a way to make it persistent, it is Linux afterall.

And ``modprobe`` is a command used to load and unload kernel modules, which are pieces of code that extend the functionality of the Linux kernel.

Once the VM launched and Ubuntu Server was installed, installing Jellyfin was so, so much easier than I had expected. It's one command, you can find it in the Jellyfin installation docs.

``curl https://repo.jellyfin.org/install-debuntu.sh | sudo bash``

Simple as that. Jellyfin is now running on my VM server. 

I needed the server to have it's own IP on the network, so I switched the VM Network Adapter type from the default NAT to Bridged. However, when I made this change, the VM no longer saw the network adapter, and couldn't connect to the internet. When I switched back to NAT, it was fine. But since I needed it on Bridged, back to the forums I went. I was seeing this error when the VM booted:

``Failed to open/create the internal network 'HostInterfaceNetworking=eno1'| (VERR_SUPDRV_COMPONENT_NOT_FOUND)``

And this is the command that fixed that error:

``sudo modprobe vboxnetflt``

``vboxnetflt`` appears to be used to load the VirtualBox network filter kernel module. Also necessary specifically for bridged networking. Again, I don't believe this command is persistent without user intervention.

Setting up the Jellyfin front end was just actually more complicated than the backend, Lol. Not harder, just more steps to go through. I hadn't actually created the folder yet to store all of my media on so I did that next.

I had only allocated about 15GB to the server (much more than needed for this) because I had intended to just store the media on the host machine and setup the server to access it through VirtualBox's "shared folders" feature. Now, a few things happened when I did this.

First off, I had mounted the shared folder to a directory in my home directory. The server could see it, Jellyfin could see it, but when I went to scan libraries, nothing would happen. As it turns out, Jellyfin does not have Read/Write permissions to your home directories (and as I later learned, it shouldn't). 

So I ended up remounting the shared folder to ``/mnt/shared``

Then Jellyfin was able to see it and started seeing the media I had put into that folder. However, the next morning when I went to add more media to the folder, I realized the server wasn't seeing the shared folder at all anymore. When I ``ls`` in ``/mnt/shared``, there was nothing in there at all. 

The VM had crashed overnight for some reason (I will get to the bottom of that if it happens again, hoping it was just a fluke). So on reboot, it didn't mount the shared folder yet. No problem, let's remount the shared folder:

``sudo mount -t vboxsf kickflix /mnt/shared``

Now it's seeing it again, cool. I go into Jellyfin's administration dashboard and rescan the library - it sees the new media, perfect. Now let's make it mount everytime the VM boots, so I don't pull my hair out again wondering what happened.

To do this, I'm going to edit the ``/etc/fstab`` file and add this line at the bottom:

``kickflix /mnt/shared vboxsf defaults 0 0``

Save that. All good.

The next steps I'm going to take with my media server are to set up it up with a domain. Or some sort of VPN configuration so myself and others can stream the media from anywhere outside of the LAN. I'm just not sure which would be easier yet for users. I want my friends and family to be able to use it if they'd like, so it needs to be pretty seamless, potentially ruling out a VPN. But a domain....maybe. 