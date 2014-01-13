---
title: "Staticly Update Number 1" 
layout: post
tags: staticly
comments: true
---

Since I have some down time, I figured that I would post an update on Staticly progress for anyone interested. Here's the [initial post](http://bringel.github.io/2013/11/12/staticly-editing-jekyll-sites-on-the-go.html) for anyone who is confused and saying "what in the world is Staticly?" Anyways, since graduating from Indiana University last month, I've had some free time until I move to my new place and start my new job. I decided to take the code that I had written during the month of November for Hack Month and basically throw it out the window. It's not that it wasn't good code, I've written much worse. I just felt that since I hadn't worked on the project in over a month, it would make more sense for me to really put the project back in my head by redesigning it. I've also been trying to prepare for the real world and go "design first". That means that classes don't get created until I've mapped them out a little bit. I'm also using this project to get back into using [Trello](http://www.trello.com) as my to do list, just because it seemed like a good idea to switch to that at the same time as scraping the project. 

Anyways, this is a progress update! So here's what's going on with Staticly right now.

1. I'm not sure if this is covered in the original post, but I'm using [AFNetworking](http://afnetworking.com) instead of [RestKit](http://restkit.org) or [OctoKit](http://octokit.github.io). Why? Because this seemed like as good of a time as any to learn how to do iOS networking with one of the most used networking frameworks in the ecosystem. I tried RestKit, and failed at it, mostly because the mapping was just a bit too much work for me to wrap my head around. Doing the JSON Deserialization myself hasn't been too bad, since each object only has a few properties that I care about right now. OctoKit looked nice, but the reactive nature of the framework wasn't something I wanted to dive into right now. Maybe for another project.

2. I've decided to focus on getting the iPad version of the app working first. It seems to me that more people are likely to want to do this kind of editing on a larger screen (most likely with an external keyboard). Luckily, I bought myself a 1st gen iPad Mini over the summer, so I have something to do development. You want a screenshot? Of course you want a screenshot!

!["She may not look like much, but she's got it where it counts, kid. I've made a lot of special modifications myself."]({{ site.url }}/images/staticly_screenshot.png)

I know that it doesn't look like too much right now, but it represents a lot of hard work on my part. Basically, what I've done so far clone a git repository using only the Github API, AFNetworking and Core Data. Each of the files that I downloaded using the API is stored in the local database. What's cool about this to me is that I didn't have to use a git library or the local filesystem directly to do this. I've been planning on elaborating on this a bit more in a separate post eventually. Now, someone is saying "But brad, this method isn't portable because it only works with Github." Well person, you're right. This isn't portable at all. I'm banking on a lot of people using Github pages to host their site, because it is by far the easiest way that I've found. This approach also brings the benefits of Github's automatic page generation whenever you push a new commit to the repository. That means that I can focus on the application right now, and not worry too much about doing my own markdown/liquid/yaml parsing.

###What's next?

So that's what's happening with Staticly right now, but what about what's coming next? Next up is the actual file editing.
If you look at the screenshot again, you can see that all of the post metadata is stuck at the top of the page (if you're familiar with jekyll), and the whole view just looks basic. I'm hoping to work on a better interface for each post/page that is going to be edited, as well as a different (better?) way to look at the configuration file. 
After that the menu is going to be reworked, along with the overall UI scheme. After that, who knows?!

You can follow the project a few ways:

[Github Repository](https://github.com/bringel/staticly)

[Trello Board for Development](https://trello.com/b/jLj8SwnF)

[Testflight Beta Tester sign up](http://tflig.ht/1d1jV4m)

For the last link, please sign up if you're interested in the project, and if you have an iPad and a Jekyll blog hosted on Github Pages. I could really use some outside input on this one.