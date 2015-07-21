---
layout: post
title: "Getting started with Octopress: on Windows and with no Ruby experience?!"
date: 2013-11-20 22:48:35 -0700
comments: true
categories: 
- Web Development
tags:
- octopress
---
So you just found [Octopress](http://octopress.org/) where you can write in [Markdown](http://daringfireball.net/projects/markdown/syntax) and get awesome free hosting for your static html via [Github pages](http://pages.github.com/). But it's built with Ruby and you are on Windows, can you still make this work?

Yes, with a little patience you definitely can! I am just getting my blog up on Octopress and thought I'd help by documenting a couple of the *gotchas* you may run into.

Head over to the [Octopress setup page](http://octopress.org/docs/setup/) to give you an outline of what you need to do.

As the setup page mentions, first you'll need to install Git if you don't already have it. I recommend the [msysgit](http://msysgit.github.io/) as it comes with a  very convenient Git Bash console. If you're unsure which installation settings to select, see the [setup page on github](https://help.github.com/articles/set-up-git) and click the "Not sure what to pick on each screen?" info box. Then head over to the [generating SSH keys](https://help.github.com/articles/generating-ssh-keys) page to make sure you can connect to Github.

Back to the Octopress setup guide, the next step is to install Ruby 1.9.3 or greater. Since we've got Windows awesomeness going on, you will want to get the [RubyInstaller for Windows](http://rubyinstaller.org/downloads/). After installation you will now have the Start Command Prompt with Ruby which you can run Ruby commands on.

Next clone the Octopress project so you have a local copy and change to that directory using your Start Command Prompt with Ruby. Here is about the point where I ran into some problems.  The setup guide has you install dependencies, so you run:

```cmd
gem install bundler
bundle install
```

But if you are lucky like me when running `bundle install` you get an error about native extensions and that you need the Development Kit.  Head on over to the [RubyInstaller Dev Kit page](https://github.com/oneclick/rubyinstaller/wiki/Development-Kit) and follow their instructions for downloading and installing the Dev Kit. After that's done, change directories back to Octopress and run those two Ruby commands again, which should both complete this time.

Following the guide, you are now ready to run the next bit `rake install` and, of course, that doesn't work either.  If you're like me and [this guy at stackoverflow](http://stackoverflow.com/q/12400185/1657324), you got a:

```cmd
rake aborted!
No such file or directory - git remote -v
```

which [the question answerer points out](http://stackoverflow.com/a/12401196/1657324) is a PATH problem. Following his link will lead to a [short and sweet description on updating your PATH](http://stackoverflow.com/a/9793850).  The basic problem here is that Ruby can't find git, and you will be updating your PATH so it can find it in the future. After the update, run that `rake install` again and it works! Hooray!

Now jump on over to [deploying to Github pages](http://octopress.org/docs/deploying/github/) and follow the instructions to get your Octopress pages pushed up to a Pages site.  Those instructions are perfect and caused me no problems so I won't bother going into them.

One last gotcha: you will generally use the two commands listed on that page `rake generate` and `rake deploy` to generate your markdown to html and push them up to Github, but one other command you'll definitely want to use is `rake preview` which will mount a webserver for you to view your site before pushing it live.  [The Octopress docs](http://octopress.org/docs/blogging/) say your webserver is mounted at http://localhost:4000, which it is, but if it's late and your brain's not working right like mine and [this stackoverflow user's](http://stackoverflow.com/q/17465404), you may try that path exactly and wonder why nothing is showing up.  If you deployed to a Github Project Page, the generated files you are trying to see are in /public/ProjectName, so you will need to add your project's name to the URL to see the preview (a la http://localhost:4000/ProjectName).

Hope some of that helps my Windows friends out there; happy blogging!