---
title: Simple Jekyll deployment using Git hooks
date: 2018-09-11 19:52:11 +0800
categories: [Deployment]
tags: [deployment, jekyll, nginx, git hooks]
---

## Local environment setup

Install [Jekyll](https://jekyllrb.com) and create a new site using the following commands:

{% highlight shell %}
gem install jekyll bundler
jekyll new blog
cd blog && bundle exec jekyll serve
{% endhighlight %}

Initialize a new Git repo
{% highlight shell %}
git init
git add .
git commit -m "Initial commit"
{% endhighlight %}

## On the server
SSH into your instance, create a folder and set up a bare Git repo
{% highlight shell %}
cd ~/
mkdir blog.git
cd blog.git
git init --bare
{% endhighlight %}

## Set up a post-receive hook

Now to set up the post-receive [Git hook](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

{% highlight shell %}
cd hooks
vim post-receive
{% endhighlight %}

paste the following code and replace the variables accordingly

{% highlight shell %}
#!/bin/bash -l

export GEM_HOME=$HOME/gems
export PATH=$GEM_HOME/bin:$PATH

TMP_GIT_CLONE=$HOME/tmp/git
GEMFILE=$TMP_GIT_CLONE/Gemfile
PUBLIC_WWW=/opt/blog

git clone $GIT_DIR $TMP_GIT_CLONE
BUNDLE_GEMFILE=$GEMFILE bundle install
BUNDLE_GEMFILE=$GEMFILE bundle exec jekyll build -s $TMP_GIT_CLONE -d $PUBLIC_WWW --trace
rm -Rf $TMP_GIT_CLONE
exit
{% endhighlight %}

What this will do is clone the repo in the folder you previously created and build the site to some destination dir, in our case `/opt/blog`

We also need to make the hook executable with `chmod +x post-receive`.

## Git remote

The last step would be to add a Git remote to push the code directly to your server

{% highlight shell %}
git remote add deploy user@server:blog.git
git push deploy
{% endhighlight %}

That's it. Your web server should be configured to serve the files from the location mentioned above.

Here is an [example Nginx config](/posts/nginx-config-for-jekyll/)