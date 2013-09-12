---
layout: post
title: An iPhone with a bigger screen should be easy.
tags:
- Apple
- Development
- development
- iOS
- iPhone
- Technology
status: publish
type: post
published: true
meta:
  _edit_last: '7639513'
  jabber_published: '1337804447'
  tagazine-media: a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";s:1:"0";s:6:"author";s:7:"7639513";s:7:"blog_id";s:8:"36130392";s:9:"mod_stamp";s:19:"2012-05-23
    20:20:43";}
---
If you've been on the internet any time within the last few months, you've heard that there's a new iPhone coming. Well at least, we think there's a new iPhone coming. As always, people have been talking about a new form factor for this new device, especially since people got the same form with the iPhone 4S in October. If Apple keeps with it's pattern-ish development scheme, we're due for one anyways. We have no way of telling what the phone might look like this time around (no prototypes lost in bars), however, the major thing that people are clamoring for on the internet is a larger screen. Personally, I'm not sure how I feel about it. I would like a larger screen as long as it doesn't change the size of the actual device that much. I'd like to still be able to fit my phone in my pocket (here's looking at you Galaxy Note owners).

<!--more-->

All this talk of a larger screen has gotten some iPhone developers in an uproar. They're tired of Apple changing things on them at the last minute and then expecting them to jump through the hoops to ship their code to a new device. It happened when Steve announce the retina display on the iPhone 4, and they're worried about what they may have to do in a hurry this time around.

The most prominent rumor at the moment is that the screen will stretch vertically but very little if at all horizontally. As a rookie iOS developer, I see this as a very minor issue. Have you ever noticed that when you flip your iPhone sideways, the UI still looks good? That's because one of the tools that Apple has given developers, called struts and springs, make sure that your app's user interface items have a few rules to follow when the height or width changes. When Apple introduced Mac OS X 10.7 last year, developers got another layout tool, called auto layout. This one allows you to define the way that your interface items should move with relation to each other. You can set a fixed amount of space or some flexible space, and the layout engine should do it's thing. I'd be willing to bet that if Apple puts a bigger screen on the iPhone we can count on them shipping auto layout to developers as well.

With both of these tools at developers fingertips, I find it hard that many iOS developers will be inconvenienced  by a larger screen. Yes there are apps out there that will be, mostly games or applications that have all custom graphics, but for the most part it should be okay. With the screen getting taller, your apps should expand to show more content. If you're using the default UIKit items in your app, Apple has probably re-written them to take advantage of the extra screen real estate. Custom classes might take some work, but some clever code in the iOS 6 runtime could take care of that.

On the subject of fragmentation, which many are also worried about, I'm also not that worried. Again, a majority of apps should be able to scale vertically and keep their layout mostly consistent. It would mean another size of images that designers would have to work on, but really it's a scaling issue, not a complete reworking, which makes a total of 5 (retina and non retina for both iPhone and iPad plus this new one). Apple has commented over and over again on Android fragmentation, so I doubt they would let it happen to their own platform. Expect some special magical code to be shipping with a larger iPhone to make it all work out. At any rate, we should hear more about this when Apple talks about the platform at WWDC, so we don't have long to wait.
