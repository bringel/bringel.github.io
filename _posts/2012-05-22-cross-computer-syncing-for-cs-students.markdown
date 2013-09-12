---
layout: post
title: Cross computer syncing for CS Students
tags:
- Development
- extensions
- git
- programming
- syncing
status: publish
type: post
published: true
meta:
  _edit_last: '7639513'
  jabber_published: '1337693963'
  tagazine-media: a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";s:1:"0";s:6:"author";s:7:"7639513";s:7:"blog_id";s:8:"36130392";s:9:"mod_stamp";s:19:"2012-05-22
    13:39:23";}
---
In the world of file syncing and storage, we have a few options. You can take your files and put them on a flash drive and carry them wherever you go. You could upload them to Dropbox or Google Drive or whatever your cloud storage provider of choice is and then download them where you need them or use the official client. Those are all fine and good options, but I want something more.

I'm a big fan of git. When I'm working on my larger assignments, I like to be able to roll back or save my spot without messing things up (by the way did you know that <a class="zem_slink" title="GitHub" href="http://github.com/blog" rel="blog" target="_blank">github</a> has free student accounts as long as you ask?). You can then push your changes up to github or <a class="zem_slink" title="BitBucket" href="http://bitbucket.org/" rel="homepage" target="_blank">bitbucket</a> or your own personal git server or any of the above options. Then you have to remember of course to pull them back down to your personal computer if you've been working in the lab and if you have changes then you have to go through stashing them and applying them or making commits and merging. It all works fine but it's tedious and you can forget to do things and then you can mess up your whole project.

So here's what I want to do. I want to be able to sync my files regardless of the system that I'm working on automatically. That's the thing I really love about dropbox is that changes get pulled down on my mac as soon as I turn it back on, so if I didn't have it with me then I can keep working. Now I know for a fact that at IU, the cluster of computers that we have accounts on called the burrow runs <a class="zem_slink" title="Red Hat Enterprise Linux" href="http://www.redhat.com/rhel/" rel="homepage" target="_blank">RHEL</a> and if you remember to enable X forwarding you can open up a window in firefox and upload your files to dropbox there. But what if you don't know that you can do that or if you don't have permission? What I'd really like to happen is for git to check the status of my repository remotes when I wake up my computer and notify me of any new commits that I need to take care of. Ideally it would look at which files had changed on each machine and do an automatic merge if I wasn't working on the same file in both places. If there were changes that needed to be taken care of, I would get notified and then I would have options as to what I wanted to do with the repositories.

You can extend git and when you type <code>git someCommandNameHere</code> git will search your path for a program called git-[someCommandNameHere] and then run it. I assume that it can be anything from a shell script to another executable compiled program.

Basically, I want everything to work like magic. Is the magic possible or is it just better to do it all the old-fashioned manual way?
