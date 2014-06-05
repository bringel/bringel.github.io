---
title: "BRExpandingViewController - A Container View Controller with two states" 
layout: post
tags:
comments: true
---
Since the pattern came on to the scene with Path and Facebook, it seems like every iOS application added some version of the slide-out "hamburger-button" menu. There's no standard class in UIKit for these types of menus, they are all custom and there are a lot of implementations floating around. 

![Relevant XKCD of course](http://xkcd.com/927/)

BRExpandingViewController is a drop in Container View controller for iPads that let you display your menu in two ways. Similar to the [Twitter](https://itunes.apple.com/us/app/twitter/id333903271?mt=8) app or the [Mint](https://itunes.apple.com/us/app/mint-personal-finance/id300238550?mt=8) app, the menu is always available in the sidebar. 