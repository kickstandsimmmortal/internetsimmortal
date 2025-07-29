--- 
title: Initiliaze Disk Windows 11
date: 2025-07-29
categories: [windows]
tags: [windows, storage, hard drives] #tags should always be lowercase
---

I had to dump some data onto a SATA drive to keep physical backups for work. And I ran into an issue of Windows 11 not seeing one of the drives. 

I knew the drive had to be initialized, but just searching Disk Management didn't pull up the actual Disk Management service I wanted, it would only pull up in the "Manage Disk and Volumes" section in Windows Settings, where it would see the drive, but the Initialize button was grayed out.

First I tried cleaning the disk with ``diskpart`` in Command Prompt as admin

``diskpart``
``list disk``
``select disk #``
``clean``
``exit``

It claimed to have successfully cleaned the disk but it still wouldn't let me initialize it from the Windows settings.

In order to get it working I had to run Disk Management from the Command Prompt as an administrator with:

``diskmgmt.msc``

This actually opened the Disk Management service I was familiar with, and allowed me to initialize the disk.

Two takes from this:

- Why does Disk Management not come up in the Windows Search results?
- Why does the initialize option work in Disk Management but not in the "Manage Disk and Volumes" section of Windows Settings?

I swear Windows 11 is still not stable but maybe I'm just too adjusted to Windows 10 and still finding my way through Windows 11