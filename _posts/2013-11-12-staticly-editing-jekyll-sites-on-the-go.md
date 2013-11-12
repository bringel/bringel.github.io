---
title: "Staticly: Editing Jekyll Sites on the Go" 
layout: post
comments: true
tags: jekyll hack_month ios programming side_project
---

It seems as though I can't stop myself from starting new projects, even though I have yet to finish any old side projects. This month (November), the [IU Computer Science Club](http://csclub.soic.indiana.edu) is having a hack month, where we try and finish some sort of project in a month. At first, I was going to continue to work on one of my existing side projects, and then I thought, "Why do something rational like that". 
At the end of last month I gave a short talk to the group about the wonders of running a website/blog on [Github Pages](www.pages.github.com) using [Jekyll](www.jekyllrb.com) (like this one that you're reading right now). One of the things that I've felt was absent from my jekyll workflow was a way to edit posts and pages on the go using my iPhone or iPad. Most people handle this by symlinking their site directory into dropbox or another cloud storage system, and editing files from there. And that's great, and it works. But it's not quite as fun. And unless you have an always on server and some terminal-wizardry, you still have to get back to your computer to publish your site. For instance, if you publish on Github pages, your site only gets regenerated when you push a commit to your github pages repository. Not ideal.

So that is where the idea from Staticly came from. Staticly is an iOS application that will allow you to edit posts, pages, drafts, and other files in your jekyll site, from wherever you are. I'm approaching this goal in a few stages.

###Stage 1

Stage 1 is focusing on github pages, standard jekyll sites. This unfortunately rules out Octopress, or anyone who generates their site locally and pushes to github pages. Right now, I have no way of generating the site on the iOS device and then pushing to github, so I'm letting github handle that for me. I'm using the [Github API](http://developer.github.com) to download the repository locally so that you can work offline. Then, when you're ready to publish, Staticly creates a commit and pushes to github, triggering a rebuild of the site. I'm limiting this stage to github hosted sites because currently there is no good way that I found to actually `git clone` something in iOS. I'm using [`AFNetworking`](afnetworking.com) to interact with the Github API, and actually save the raw git data into Core Data. 
Originally (read 11 days ago) I decided to use [RestKit](http://restkit.org) over [OctoKit](http://octokit.github.io) because I thought that it would be easier since RestKit has built in support for mapping JSON back to Core Data entities. However, I just didn't have enough time to learn what was going on in RestKit and debug some errors that I was running into. So I switched over to a pure AFNetworking implementation yesterday, and I'm already running into fewer issues. That's not to say that RestKit and OctoKit are unusable, I just didn't have enough time to really wrap my head around them, and I might revisit them later. This implementation alone is probably worth two blog posts once I get it up and running, One for integrating AFNetworking with Core Data, and one for reimplementing the underlying git file structure in Core Data (for those familiar with the plumbing of git, yes I'm implementing blobs trees and commits because I'm crazy).

Stage 1 also includes a Markdown (and small parts of a Liquid) parser. My plan for stage 1 is to just parse the markdown pages into html, and not worry about implementing all of what jekyll does. My theory is that people know what their website layout looks like, so if we can generate and preview the actual content that winds up in the `{{ content }}` tag, that *might* be good enough for stage 1.

###Stage 2

I'd like to extend the previewing capabilities of Staticly in stage two to handle more Liquid tags and control flow. I'm hoping to include support for more hosts in stage two. I know that a lot of people are hosting on heroku. Octopress support might be able to squeeze its way into stage 2 as well.

###Stage 3

Finally in stage three you should be able to view your entire site in preview before generating it. This means eithe reimplementing pretty much all of jekyll, or finding a way to run jekyll on iOS (which would be no easy task). 

###Suggestions

Staticly is going to be a major work in progress over the next few weeks, and I'll try and post updates as they come. If you are a jekyll user, or you're thinking about it and Staticly sounds like something you might be interested in, I'd love to hear from you. I'm fully open to suggestions, and requests to beta test provided that you're running iOS 7. The code is posted here on [Github](www.github.com/bringel/staticly)