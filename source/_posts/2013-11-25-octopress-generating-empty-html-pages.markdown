---
layout: post
title: "Octopress generating empty html pages"
date: 2013-11-25 22:05:19 -0700
comments: true
categories: 
- Web Development
tags:
- octopress
---
So you probably just tried a `rake generate` on your Octopress blog only to end up with a blank index.html in your public folder and a few empty posts in your blog folder, ya?

When this occurred for me, the problem happened right as I started trying out the fancy syntax highlighting for code blocks.  Posts with plain vanilla code blocks generated just fine, but as soon as I added languages for highlighting at the start of each block I saw empty posts/pages.  After many searches for almost every synonym of 'empty' you can think of, I finally solved my problem through a combination of the following:

## Check if Python is installed

"But Octopress is in Ruby, why do I need Python?"  Octopress uses [Pygments](http://pygments.org/) for syntax highlighting, which is built in Python, and it uses the pygments.rb gem to use said Python file.

## Ensure Pygments is installed in Python 

This was one of my problems.  I already had Python from some earlier messing around, but didn't know I needed to separately download Pygments and install it.  Well, ya do.  Here's the [download site](http://pygments.org/download/), or you can use some `easy_install` fanciness if you prefer.

## Make sure your PATH for Python is set correctly

Another one of my problems.  I happened upon this from a [stackoverflow user having similar problems with code blocks](http://stackoverflow.com/a/14202515/1657324).  Here are some good instructions for [updating PATH in Windows](http://stackoverflow.com/a/4855685/1657324) from another SO question, and yet another SO answer on [possible mistakes when updating PATH](http://stackoverflow.com/a/12592280/1657324).  Most useful to me: be sure to **exit and restart the window you are testing in**.  I didn't do that, and it caused a lot more headaches.

## Update pygments.rb to a newer version

```cmd
bundle update pygments.rb
```

Not sure if I really needed to do this, but one of my searches led me to this [SO answer on octopress generating blank files](http://stackoverflow.com/a/14088936/1657324), and figured updating pygments.rb couldn't hurt.  Running the above resulted in pygments.rb updating from 0.3.4 to 0.3.7.  I then updated the version in my Gemfile in the octopress main folder and was good to go.[^1]

Doing all this did not initially solve my problem, because I never exited my command-line window to let my PATH do its magic.  But the next day everything was working again, and I was scratching my head (until I found that comment on closing and re-opening the window). Hopefully something here will help you with this horrible problem!

[^1]: incidentally, I am so new to Ruby I didn't know how to update a Gemfile.  If you are in the same boat, just open Gemfile in a text editor to see all the gems Octopress is using and their version.  When you update a gem with `bundle update`, you download the new version but also keep a copy of the old version in your gems folder.  So to update what Octopress uses, you need to update the version number in your Gemfile