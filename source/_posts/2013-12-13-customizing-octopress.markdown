---
layout: post
title: "Customizing Octopress"
date: 2013-12-13 02:40:21 -0700
comments: true
categories: 
- Web Development
tags:
- octopress
---
I recently made a few customizations to my Octopress environment using resources/blog articles scattered about the web; all were surprisingly easy and definitely added value.  Here are my favorites if you want to take a stab at any of these yourself:

## [Hiding specific categories of posts from the front page](http://arshad.github.io/blog/2012/05/10/recipe-hiding-posts-from-the-octopress-front-page/)  

I particularly wanted to do this for my Snippets category, as sometimes I want to take notes or describe the logic of a concept (or simply have a basic example that I can copy in the future), but I don't particularly want that showing up as the top article on my front page either.  Best of both worlds by following the simple changes in this article.

## [Change the preview server to Ruby Thin Server](http://blog.geoffpetrie.com/blog/2012/10/14/octopress-change-the-default-preview-server/)  

I did this trying to solve a different problem; it didn't help then but I liked the server so much that I've stuck with it. Very easy to get it up and running too.

## [Footnote styling](http://hiltmon.com/blog/2013/05/08/octopress-now-has-footnotes/)  

I really liked the idea of using footnotes for my sources, but by default they are rather gigantic and widely spaced.  Some simple additions to `sass/custom/_styles.css` and your footnotes look like they are supposed to: compact and well-spaced.  I'll even add an example footnote[^1] for you to see what these changes look like.

<!-- more -->

## [Filter repositories from Github sidebar](http://blog.codebykat.com/2013/05/20/three-octopress-tweaks/#github-aside) 

As the author of that linked post so aptly points out, it doesn't make a lot of sense to show off the repository your blog is on if you are being hosted by Github Pages.  Also, some projects you just don't want to show off, and a few lines of code will make sure they don't appear under the Github Repos of your blog.

## [Use Monokai color scheme for code block syntax highlighting](https://github.com/ragle/Monokai-Sublime-Text-Octopress) 

Seems like lots of people want to slightly alter the built in syntax highlighting.  I like the Monokai theme from Sublime Text 2, and luckily I found a [nice patch](https://github.com/ragle/Monokai-Sublime-Text-Octopress) that will switch to that color scheme without much additional work.  You can also find great, detailed posts if you are more interested in the [Twilight color scheme](http://blog.alestanis.com/2013/02/04/octopress-and-the-twilight-color-scheme/) or a more [Github-style color scheme](http://blog.codebykat.com/2013/05/23/gorgeous-octopress-codeblocks-with-coderay/).  Keep in mind though that some of these will require you to change your markdown processor and swap in a different syntax highlighter.  I preferred to stick with the default Pygments syntax highlighting and rdiscount markdown processor, and I was lucky to find a great resource already created.  Check out the prettiness:

{% include_code sample.js %}

## [Add tweets to the sidebar without the ugly widget](http://blog.jonathanrwallace.com/blog/2013/09/21/octopress-love-twitter-update/) 

I don't tweet a lot, but when I do, I want them to look like they belong on the page.  After Twitter changed its API to require authentication, it effectively got dropped from Octopress.  But a [nice Github contributor](https://github.com/imathis/octopress/pull/1311) figured out a way to continue displaying tweets.  Essentially, you create a Twitter widget and pull out the data-widget-id from the generated code and plop that into the _config.yaml file.  Looks great on the sidebar too!

[^1]: I really like these footnotes.  You can even put `code blocks` in them and everything.  Great place to put [ancillary links](http://imgur.com/r/cats/YYYC3bW) that people may or may not care to look at/read.