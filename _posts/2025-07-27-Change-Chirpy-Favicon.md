--- 
title: Customize Chirpy's Favicon
date: 2025-07-27
categories: [selfhosting]
tags: [css, selfhosting, website] #tags should always be lowercase
---

This was a little weirder than I thought it'd be because the Chirpy's official documentation is kind of vague once you get past the favicon generator steps.

In the documentation it says to just replace all of Chirpy's ``.png`` and ``.ico`` files with the files you get from the favicon generator and then just rebuild the site. 

However, there's a bit more to it than that.

Yes, you do have to replace those files with your own. But then you have to navigate to ``/_includes/favicons.html`` and edit there to actually get it working. The default setup has two lines that say ``type=:image/png"`` in them.

![faviconshtml](/assets/img/faviconshtml.png)

As you can see on lines 9 and 10 there are two ``png`` type file references. By default the sizes of these images are ``32x32`` and ``16x16`` respectively. Mine says ``96x96`` for both because I was testing something and the favicon image generator only gave me that size. 

Now if you look at the favicon generator's documentation it shows you a bunch of lines you need to add to the ``<head>`` sections of your pages. 

![favgenerator](/assets/img/favgenerator.png)

Notice there is only one ``.png`` file type reference in this code, but two of them in the ``favicons.html`` file. So I replaced line 9 with the correct file name, which in my case was ``favicon-96x96.png`` and then I replaced line 10 with the ``favicon.svg`` file the generator provided. 

![endresult](/assets/img/endresult.png)

Notice the change on line 10.

Everything else was named correctly so it stayed the same. But I struggled with this more than I anticipated, and I think Chirpy's documentation could be a little more detailed in it's instructions to do this.

Regardless, it's done, maybe if someone runs into the same issue they'll find the answers here.