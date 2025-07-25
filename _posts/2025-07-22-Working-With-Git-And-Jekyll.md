--- 
title: Working with Git and Jekyll
date: 2025-07-22
categories: [selfhosting]
tags: [homelab, selfhosting, website] #tags should always be lowercase
---

I'm sure I won't really need to refer to this myself much as I post to this site more often, but as this is all new to me, I figured I should just write down the workflow to clone the site's Git repo to whatever machine I'm working on it from, spin up a local jekyll server and make changes before pushing new content (or a broken website) to GitHub.

To start with I just cloned my site's repository into a directory I chose

```
git clone https://github.com/kickstandsimmmortal/internetsimmortalv2.git
```

Once that was done, I verified I had all the required packages to run the site locally so I could make changes safely

```
ruby -v
jekyll -v
bundle -v
```

I moved into the repo's directory, and then installed the depenencies for my project with:

```
bundle install
```

Once that was done, I bundled and spun up the site locally with:

```
bundle exec jekyll serve
```

Once I made the changes and I was happy with them - in this case, this post, lol- I committed it to GitHub

```
git status
git add .
git commit -m "Describe your changes here"
git push origin main
```

It should be noted that in that last command, "main" could be replaced with whatever branch of the repo you're making changes to

That's pretty much it. I need to figure out how to customize this site a bit more because right now the color scheme is quite boring (particularly with the colors). But that'll probably be for another day because a few google search results have scared me away from doing it at 11:50pm on a Tuesday night.