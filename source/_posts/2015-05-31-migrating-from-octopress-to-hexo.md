title: "Migrating from Octopress to Hexo"
date: 2015-05-31 19:00:28
categories: 
- Web Development
tags:
- hexo
- octopress
- static site generators
---

One of the original reasons  I started using Octopress for blogging is I thought it would increase my motivation to learn Ruby.  However, after spending some time mucking about with [various Octopress hacks](./2013/12/13/customizing-octopress/), I decided Ruby just might not be for me.

So I did some searching around to see what other [static site generators](https://www.staticgen.com/) might interest me, and low and behold I stumbled onto [Hexo](http://hexo.io/).  Hexo is written in JavaScript, and runs on the Node.js and is growing in popularity.  I've tinkered with Node.js some in the past, and since JavaScript is a language I'm more comfortable with I decided to take the plunge.  Here are some things I found that may help should you decide to migrate this way too.

## Content Migration

Incredibly easy, I loved it.  Simply move your markdown files from Octopress `source/_posts` to your Hexo `source/_posts`.  Done, posts are ready to go.  One other setting you may want to modify is the default name format for new posts.  Simply open up the `_config.yml` file in Hexo's main directory and change `new_post_name` to:

```
new_post_name: :year-:month-:day-:title.md
```

This will give your new files the same file name format as the Octopress default.

<!-- more -->

## Basic Commands

All commands are very similar to Octopress except you'll use the keyword `hexo` instead of `rake`.  Some examples:

* New post: `hexo new "post title"`
* New page: `hexo new page "page name"`
* Start server: `hexo server`
* Generate files in the public folder: `hexo generate`
* Deploy files to Github: `hexo deploy`

One big plus for Hexo is when previewing posts using `hexo server`, you don't need to go through the process of generating files first.  Made a change to one of the `_config.yml` files, or trying out a new widget?  Simply start up the server and see what it looks like!

## Optional Add-Ins

If, like me, you are a big fan of the little things like footnotes, strikethroughs and simple tables, you can install a separate markdown renderer to keep working with all that goodness.  My markdown renderer of choice is [Markdown-it](https://github.com/celsomiranda/hexo-renderer-markdown-it), which allows all the things I mentioned (and also supports Github Flavored Markdown, which I know many are a fan of).

## Roll Your Own Widgets

One thing that's nice about messing with asides in Octopress is it aides in transitioning to editing widgets in Hexo.  Some themes, like the modified Light theme I'm currently using, come with pre-made sidebar widgets ready to go.  I was not familiar with the .ejs templating in Hexo, but after looking through a few you can easily get a feel for their flow.

I've been messing around with a Twitter widget that runs off a timeline, similar to the hack-y functionality on my Octopress site.  Using [this proposed pull request to Octopress to fix Twitter plugins](https://github.com/imathis/octopress/pull/1311/files) I made some edits to .ejs files to construct it.  If you'd like to try this yourself:

* Enable twitter under widgets, and set variables for your twitter account/timeline in your themes `_config.yml`:

```raw themes/light/_config.yml
widgets:
- search
- category
- tag
- twitter

twitter:
  username: stueth
  show_replies: false
  tweet_count: 3
  widget_id: 411983651094536192
  follow_button: true
  show_follower_count: false
```

* Add the following to the bottom of your themes layout/_partial/after_footer.ejs file:

```raw themes/light/layout/_partial/after_footer.ejs
<% if (config.twitter_follow_button){ %>
<script type="text/javascript">
(function(){
  var twitterWidgets = document.createElement('script');
  twitterWidgets.type = 'text/javascript';
  twitterWidgets.async = true;
  twitterWidgets.src = '//platform.twitter.com/widgets.js';
  document.getElementsByTagName('head')[0].appendChild(twitterWidgets);
})();
</script>
<% } %>

<% if (config.twitter_username){ %>
<script type="text/javascript">
(function(d,s,id){
  var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}})(document,"script","twitter-wjs");
</script>
<% } %>
```

* Create this twitter.ejs file in your theme's _widget folder:

```raw themes/light/layout/_widget/twitter.ejs
<% if (config.twitter_username){ %>
<div class="widget twitter">
  <h3 class="title">Latest Tweets</h3>
  <ul id="tweets">
    <a class="twitter-timeline" data-dnt="true" href="https://twitter.com/<%= config.twitter_username %>"  
      data-widget-id=<%= config.twitter_widget_id %>  data-link-color="#1863a1" 
      data-tweet-limit="<%= config.twitter_tweet_count %>" 
      data-chrome="noheader nofooter transparent noscrollbar">Tweets by @<%= config.twitter_username %></a>
  </ul>
  <% if (config.twitter_follow_button){ %>
    <a href="http://twitter.com/<%= config.twitter_username %>" class="twitter-follow-button" 
      data-show-count="<%= config.twitter_show_follower_count %>">Follow @<%= config.twitter_username %></a>
  <% } %>
</div>
<% } %>
```

* Then add any styling changes in you theme's source/css/_partial/sidebar.styl