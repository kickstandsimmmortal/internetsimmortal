--- 
title: Windows 11 File Explorer Slow/Freezing Fix
date: 2025-07-29
categories: [Windows]
tags: [windows, file explorer, troubleshooting] #tags should always be lowercase
---

Another issue I've run into with Windows 11 is after some time, File Explorer begins to be unresponsive and slow. Lots of hangtime in loading and the program will stop responding.

The solution that seems to work best at the moment is to open ``Run`` and enter:

``shell:recent\AutomaticDestinations``

Delete all files in this folder.

Open ``Run`` again and enter:

``shell:recent\CustomDestinations``

Delete all files in this folder as well. Then open CMD as Admin and run the following commands:

``DISM.exe /Online /Cleanup-image /RestoreHealth``

``sfc /scannow``

And then restart the computer.

My understanding of the two folders you have to delete files from is that they are some sort of cache for recently accessed files. I guess if this builds up too much it can cause File Explorer to hang while it goes through them. 

The irony is it seems these files are meant to speed up File Explorer.

As for the ``DISM.exe`` command, it is meant to manage and repair Windows Images.

The ``/Online`` parameter tells it to target the currently running OS. The ``/CleanupImage`` parameter tells it to do exactly as the parameter says, cleanup the image. And the ``/RestoreHealth`` parameter scans the image for corruption and attempts to repair itself by downloading necessary files from Windows Update.

``sfc /scannow`` scans all protected system files, verifies the integrity of them, and replaces any corrupt or missing files with correct versions from a cached copy located in ``C:\Windows\System32\dllcache``.

All of these commands and actions contribute to cleaning up the image and effectively speeding things up. Every computer I've run these commands on has had very noticeable gains in efficiency and speed.