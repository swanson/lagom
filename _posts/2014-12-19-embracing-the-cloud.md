---
layout: post
title: Embracing the cloud
---

For a long time, I ran a blog, wiki and static website on a VPS which I managed. As I finished education and started in the work force, I realised that managing a server in my spare time just wasn't fun anymore, and the website and blog fell into disarray. You can't spend that much time worrying about SSH hardening, config management for Apache and other medial tasks when all you want to do is write a blog post or update your resume.

Alongside this, I realised I spend a lot of time in Git, a lot of time editing and parsing JSON and a lot of time writing ruby code and it was becoming second nature. Blame the DevOps movement if you want, but the "System Administrator" role just isn't the same thing it was even 5 years ago when I started out, and I wanted my time to be spent doing these things that I did daily, rather than worrying if my server had been hacked and regular patches.

Today I decided I'd had enough, and went "full devops" on my personal spaces. In all honesty, I kinda wish I'd done it sooner. Writing this blog post was just about the easiest thing I've done in a long time. Here's what I did.

## Personal Space & Blog

The first thing I need to do was replace the old [Wordpress](https://wordpress.com) blog with something I can actually use on a daily basis. I wanted something that supported [Markdown](http://daringfireball.net/projects/markdown/), as it's a format I'm familiar with and use every day. I also wanted something that would work with minimal fuss, and could be hosted elsewhere. [Jekyll](http://jekyllrb.com/) filled all these criteria, and when I discovered (like many before me) that I could host the content on [Github Pages](https://pages.github.com/), use a CNAME from my main URL, AND it was written in ruby, it was a no brainer. Getting started was really easy. 

- First, pick a theme. I went with the excellent [lagom](https://github.com/swanson/lagom) theme
- Fork it in Github
- Rename the repo to <gitusername>.github.io
- Edit the theme as required, adding your name and personal data
- Push back to the repo

It was so easy, I wondered why the hell I'd been messing around with PHP/MySQL and Apache!

Once I'd done that, I wanted to add this post. So I added a _posts directory, created a file with the format YYYY-MM-DD-title.md and started hacking away. 

It's useful to be able to see what your posts look like while you're writing them, so I installed the jekyll gem and and then served up the page localy

{% highlight bash %}
sudo gem install jekyll
bundle install
bundle exec jekyll serve
{% endhighlight %}

You can see the post as you edit it at http://localhost:4000.


