--- 
title: Customizing Chirpy's CSS
date: 2025-07-24
categories: [selfhosting]
tags: [css, selfhosting, website] #tags should always be lowercase
---

When I first set up this site I didn't realize there were two repo's for Chirpy - one repo for the full website with all of the customization options and one repo that was a "starter". 

That's my own fault for not reading the documentation as thoroughly as I should have (a problem I often have lol).

Anyways, when I decided this site was too bland and boring in it's default "starter" state, I went looking into the code for CSS files that I could alter and make some changes to make the site a little more mine.

This led me to discovering I had cloned the starter repo and not the full blown chirpy repo I needed in order to customize the things I wanted to change. 

So now it was time to actually clone the correct repo, copy my config file and the posts I had already put up into this new site directory, and then spin up a local server so I could play around with it before going live.

The first place I looked was ``assets/css`` where I found a ``jekyll-theme-chirpy.scss`` file. The problem was, it was empty, and while I know some CSS, I needed to figure out how the HTML elements were labeled.

![assets-folder](/assets/img/assetsfolder.png)

So I viewed each pages source and scrolled until I found what I was looking for.

I was able to change the title on the sidebar of this page from that gray to this cool ``greenyellow`` color, that I ended up using in a few other spots as well.

That was labeled as ``site-title``, added that to my ``.scss`` file and boom, now we've got some color

![source](/assets/img/inspect2.png)

Did the same thing the icons, the headers, the regular text, and the code text as well with no issues until...I got to the preview post boxes for the home page. 

![post-preview](/assets/img/post_preview.png)

No matter what I entered for the class or even flagging that CSS as ``!important``, it just would not respond to the changes I wanted to make.

So I looked deeper into the code, eventually finding a file called ``_dark.scss`` in ``/_sass/themes/``

![darkcss](/assets/img/darkcss.png)

This file actually had a lot of completed code, and although the labeling wasn't entirely clear to me, after just messing with anything that sounded like it could be the elements I wanted to edit, I eventually found the text I wanted to change under ``text-muted-color`` and ``heading-color``

![css](/assets/img/css.png)

This is also where I was able to change the color of the ``highlighted`` text. There's another ``.scss`` file here that presumably affects the light mode version of this site, but since I configured the site to only use dark mode, I did not mess with that at all.

I'm sure I'm missing a couple of things, but the jist of it all is here. It took some tweaking, some inspect element and some guessing but I've managed to get it to a pretty good spot I'm happy with for now.

