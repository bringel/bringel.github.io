---
title: "Faking Git using Core Data and the Github API" 
layout: post
tags: [objective-c, git, github, coredata] 
comments: true
---
As far as personal blogs and websites go, I'm a pretty big fan of Jekyll. I think that for people who are comfortable using the command line, there's really no reason to go with anything else. I also really like the fact that you can use Github to host and build your site. But I also think that if you use Jekyll to build your site, you should be able to work on it on your iPhone or iPad too, which is obviously why I'm trying to build [Staticly]({% post_url 2014-01-13-staticly-update-number-1 %}) (shameless plug, that post isn't really relevant to the cool things in this post). 

Anyways, There were a few options to pick from to work with Github in order to work with jekyll sites. I could work with [libgit/objective-git](http://libgit2.github.com) which would have been a good choice, and is one that I will probably go with if I need to support non-github repositories (which I probably will). I could use [OctoKit](http://octokit.github.io) which is the Github provided library for working with their API. Or I could roll my own and use [AFNetworking](http://afnetworking.com) to make the API Requests.

Obviously I went with option number three, otherwise I wouldn't be writing this post. Why? Well because reasons and stuff

* I thought that it would be easier to not use the local filesystem for this project. I know that Linus has his reasons for the way that git is designed, but I figured this would be a good learning opportunity for me to not do it that way.

* I also prefer to work with my own objects when I can. Should I get used to working with other people's code? Yes. Am I going to do it right now? Probably not

* OctoKit is nice, but I haven't totally wrapped my head around [Reactive Cocoa](https://github.com/ReactiveCocoa/ReactiveCocoa) which OctoKit is built on.

* Offline performance in mobile applications is very important to me. I always feel that people should be able to do work without a network connection, which means that I want my things persisted to disk. I'm most familiar with using Core Data for this (despite the numerous posts on the internet telling me not to). 

* Sometimes it's fun and a great learning experience to build out a lot of things yourself.

In order to really sink my teeth into the project, and to figure out how to replicate the git database structure in my Core Data design, I spent a lot of time going through the git documentation. Specifically [Chapter 9](http://git-scm.com/book/en/Git-Internals) of the git book. I must say, I learned a whole lot about the way that files are stored in git by reading this, and I might even say that it made me a better git user in the process. But enough rambling. Instead of posting the headers, I'll just post the current state of the Core Data Object Model:

![Graph View of the Core Data Managed Object Model]({{ site.url }}/images/core_data_object_graph.png)

I've created a class called `SLGithubSessionManager` which is a subclass of `AFHTTPSessionManager` which handles setting up AFNetworking to use the Github API.

{% highlight objective-c linenos %}
//
//  SLGithubSessionManager.h
//  Staticly
//
//  Created by Bradley Ringel on 1/3/14.
//  Copyright (c) 2014 Bradley Ringel. All rights reserved.
//

#import "AFHTTPSessionManager.h"
#import "SLUser.h"
#import "SLSite.h"
#import "SLCommit.h"
#import "SLTree.h"
#import "SLBlob.h"

@interface SLGithubSessionManager : AFHTTPSessionManager

@property (strong, nonatomic) NSManagedObjectContext *managedObjectContext;
@property (strong, nonatomic) NSString *clientID;
@property (strong, nonatomic) NSString *clientSecret;

+ (instancetype)sharedManager;
- (SLUser *)currentUser;
- (SLSite *)currentSite;

- (SLCommit *)commitWithSha:(NSString *)sha;
- (SLTree *)treeWithSha:(NSString *)sha;
- (SLBlob *)blobWithSha:(NSString *)sha;

@end
{% endhighlight %}
*`SLGithubSessionManager` Header file*

{% highlight objective-c linenos %}
//
//  SLGithubSessionManager.m
//  Staticly
//
//  Created by Bradley Ringel on 1/3/14.
//  Copyright (c) 2014 Bradley Ringel. All rights reserved.
//

#import "SLGithubSessionManager.h"
#import "SLAppDelegate.h"
@interface SLGithubSessionManager()

@end

@implementation SLGithubSessionManager

- (id)initWithBaseURL:(NSURL *)url sessionConfiguration:(NSURLSessionConfiguration *)configuration{
	self = [super initWithBaseURL:url sessionConfiguration:configuration];
	if(self){
		NSDictionary *apiInformation = [[NSDictionary alloc] initWithContentsOfURL:[[NSBundle mainBundle] URLForResource:@"githubapi" withExtension:@"plist"]];
		self.clientID = [apiInformation objectForKey:@"clientID"];
		self.clientSecret = [apiInformation objectForKey:@"clientSecret"];
		self.requestSerializer = [AFJSONRequestSerializer serializer];
		self.responseSerializer = [AFJSONResponseSerializer serializer];
		[self.requestSerializer setValue:@"application/vnd.github.v3+json" forHTTPHeaderField:@"Accept"];
		self.managedObjectContext = [(SLAppDelegate *)[[UIApplication sharedApplication] delegate] managedObjectContext];
	}

	return self;
}

+ (instancetype)sharedManager{

	static SLGithubSessionManager *manager;
	static dispatch_once_t onceToken;
	dispatch_once(&onceToken, ^{
		NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
		configuration.requestCachePolicy = NSURLRequestReloadIgnoringLocalCacheData;
		manager = [[SLGithubSessionManager alloc] initWithBaseURL:[[NSURL alloc] initWithString:@"https://api.github.com"] sessionConfiguration:configuration];

	});

	return manager;
}

- (SLUser *)currentUser{
	NSPredicate *currentPredicate = [NSPredicate predicateWithFormat:@"currentUser == %@", @(YES)];
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"SLUser"];
	request.predicate = currentPredicate;

	NSError *error;
	NSArray *users = [self.managedObjectContext executeFetchRequest:request error:&error];

	if(users.count == 1){
		//if this isn't the case then we've got a problem
		return [users firstObject];
	}
	else{
		return nil;
	}
}

- (SLSite *)currentSite{
	NSPredicate *currentPredicate = [NSPredicate predicateWithFormat:@"currentSite == %@", @(YES)];
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"SLSite"];
	request.predicate = currentPredicate;

	NSError *error;
	NSArray *sites = [self.managedObjectContext executeFetchRequest:request error:&error];

	if(sites.count == 1){
		//if this isn't the case then we've got a problem
		return [sites firstObject];
	}
	else{
		return nil;
	}
}

- (SLCommit *)commitWithSha:(NSString *)sha{
	NSPredicate *pred = [NSPredicate predicateWithFormat:@"sha == %@", sha];
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"SLCommit"];
	request.predicate = pred;
	NSError *error;

	NSArray *commits = [self.managedObjectContext executeFetchRequest:request error:&error];
	if(commits.count == 1){
		//i hope that this is the case
		return [commits firstObject];
	}
	else{
		return nil;
	}
}

- (SLTree *)treeWithSha:(NSString *)sha{
	NSPredicate *pred = [NSPredicate predicateWithFormat:@"sha == %@", sha];
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"SLTree"];
	request.predicate = pred;
	NSError *error;

	NSArray *trees = [self.managedObjectContext executeFetchRequest:request error:&error];
	if(trees.count == 1){
		return [trees firstObject];
	}
	else{
		return nil;
	}
}

- (SLBlob *)blobWithSha:(NSString *)sha{
	NSPredicate *pred = [NSPredicate predicateWithFormat:@"sha == %@", sha];
	NSFetchRequest *request = [[NSFetchRequest alloc] initWithEntityName:@"SLBlob"];
	request.predicate = pred;
	NSError *error;

	NSArray *blobs = [self.managedObjectContext executeFetchRequest:request error:&error];
	if(blobs.count == 1){
		return [blobs firstObject];
	}
	else{
		return nil;
	}
}

@end
{% endhighlight %}
*`SLGithubSessionManager` Implementation File*

You can see that I've got `SLGithubSessionManager` doing a few other things to make life easier. In order to prevent creating duplicate objects when they come in from Github, the `commitWithSha:`, `treeWithSha:` and `blobWithSha:` will return the appropriate object if one exists. Since every time I'm dealing with objects that may or may not exist in my database already, there is an `SLGithubSessionManager` around, I figured this would be the easiest place to access those convenience methods.

Now on to the fun stuff. I'll skip logging in to Github and getting the OAuth token because that's old news. 

Here's how you get all of the authenticated users repositories:

{% highlight objective-c linenos %}

NSString *fetchString = [NSString stringWithFormat:@"/users/%@/repos", self.currentUser.username];

SLGithubSessionManager *manager = [SLGithubSessionManager sharedManager];

[manager GET:fetchString parameters:@{@"access_token" : self.currentUser.oauthToken}
	 success:^(NSURLSessionDataTask *task, id responseObject) {
		 NSHTTPURLResponse *response = (NSHTTPURLResponse *)task.response;
		 if(response.statusCode == 200){
			 NSArray *responseData = responseObject;
			 self.sites = [responseData copy];
			 
			 [self.tableView reloadData];
			 
		 }
	 }
	 failure:^(NSURLSessionDataTask *task, NSError *error) {
		 
	 }];
{% endhighlight %}

The API returns JSON, which `AFNetworking` then serializes into an array of `NSDictionary` objects, called `responseObject`. To avoid creating an `SLSite` object for each repository that is returned, I simply use the dictionary to show that information to the user, and then create the object once the user has selected a row in a `UITableView`.

The method that fetches the branches of the selected repository is pretty much identical, so I won't post that.

Here's the best part, cloning an entire repository into a Core Data database:

{% highlight objective-c linenos %}
NSPredicate *currentPredicate = [NSPredicate predicateWithFormat:@"defaultBranch == %@", @(YES)];
NSOrderedSet *defaultBranch = [[[[SLGithubSessionManager sharedManager] currentSite] branches] filteredOrderedSetUsingPredicate:currentPredicate];
SLCommit *head = [[defaultBranch firstObject] commit];

SLGithubSessionManager *manager = [SLGithubSessionManager sharedManager];
NSString *username = [[manager currentUser] username];
NSString *siteName = [[manager currentSite] name];
NSString *token = [[manager currentUser] oauthToken];

void (^blobSuccessBlock)(NSURLSessionDataTask *, id) = ^void (NSURLSessionDataTask * task, id responseObject){
	NSHTTPURLResponse *response = (NSHTTPURLResponse *)task.response;
	if(response.statusCode == 200){
		NSDictionary *blobData = responseObject;
		SLBlob *blob = [manager blobWithSha:[blobData objectForKey:@"sha"]];
		blob.content = [[NSData alloc] initWithBase64EncodedString:[blobData objectForKey:@"content"] options:NSDataBase64DecodingIgnoreUnknownCharacters];
		NSError *error;
		[self.managedObjectContext save:&error];
	}
};

void (^blobFailBlock)(NSURLSessionDataTask *, NSError *) = ^void (NSURLSessionDataTask *task, NSError *error){
	NSLog(@"%@", error);
};
void (^treeFailBlock)(NSURLSessionDataTask *, NSError *) = ^void (NSURLSessionDataTask *task, NSError *error){
	NSLog(@"%@", error);
};

void (^__block treeSuccessBlock)(NSURLSessionDataTask *, id) = ^void (NSURLSessionDataTask *task, id responseObject){
	NSHTTPURLResponse *response = (NSHTTPURLResponse *)task.response;

	if(response.statusCode == 200){
		NSDictionary *treeData = responseObject;
		NSArray *objects = [treeData objectForKey:@"tree"];
		SLTree *rootTree = [manager treeWithSha:[treeData objectForKey:@"sha"]];
		for(NSDictionary *gitObject in objects){
			//Check to see whether this is a tree or a blob, and do the right thing
			//dont forget to set the relationship in all of them
			if([[gitObject objectForKey:@"type"] isEqualToString:@"tree"]){
				NSFetchRequest *treeRequest = [[NSFetchRequest alloc] initWithEntityName:@"SLTree"];
				NSPredicate *treeShaPredicate = [NSPredicate predicateWithFormat:@"%K LIKE %@", @"sha", [gitObject objectForKey:@"sha"]];
				treeRequest.predicate = treeShaPredicate;
				NSError *error;
				NSArray *trees = [self.managedObjectContext executeFetchRequest:treeRequest error:&error];
				
				SLTree *tree;
				if(trees.count == 0){
					tree = [NSEntityDescription insertNewObjectForEntityForName:@"SLTree" inManagedObjectContext:self.managedObjectContext];
				}
				else{
					tree = [trees firstObject];
				}
				tree.sha = [gitObject objectForKey:@"sha"];
				tree.path = [gitObject objectForKey:@"path"];
				tree.parent = rootTree;
				
				[self.managedObjectContext save:&error];
				NSString *treeGetString = [NSString stringWithFormat:@"/repos/%@/%@/git/trees/%@",username,siteName,tree.sha];
				[manager GET:treeGetString parameters:@{@"access_token" : token} success:treeSuccessBlock failure:treeFailBlock];
			}
			else{
				NSFetchRequest *blobRequest = [[NSFetchRequest alloc] initWithEntityName:@"SLBlob"];
				NSPredicate *blobShaPredicate = [NSPredicate predicateWithFormat:@"%K LIKE %@", @"sha", [gitObject objectForKey:@"sha"]];
				blobRequest.predicate = blobShaPredicate;
				NSError *error;
				NSArray *blobs = [self.managedObjectContext executeFetchRequest:blobRequest error:&error];
				
				SLBlob *blob;
				if(blobs.count == 0){
					blob = [NSEntityDescription insertNewObjectForEntityForName:@"SLBlob" inManagedObjectContext:self.managedObjectContext];
				}
				else{
					blob = [blobs firstObject];
				}
				blob.sha = [gitObject objectForKey:@"sha"];
				blob.path = [gitObject objectForKey:@"path"];
				blob.tree = rootTree;
				
				[self.managedObjectContext save:&error];
				
				NSString *blobGetString = [NSString stringWithFormat:@"/repos/%@/%@/git/blobs/%@", username, siteName, blob.sha];
				[manager GET:blobGetString parameters:@{@"access_token" : token, @"encoding" : @"base64"} success:blobSuccessBlock failure:blobFailBlock];
			}
		}
	}
};



void (^commitSuccessBlock)(NSURLSessionDataTask *, id) = ^void (NSURLSessionDataTask *task, id responseObject){
	NSError *error;

	NSHTTPURLResponse *response = (NSHTTPURLResponse *)task.response;
	if(response.statusCode == 200){
		NSDictionary *commitData = responseObject;
		//loop through all the parents and get those
		for(NSDictionary *parent in [commitData objectForKey:@"parents"]){
			NSFetchRequest *commitRequest = [[NSFetchRequest alloc] initWithEntityName:@"SLCommit"];
			NSPredicate *commitShaPredicate = [NSPredicate predicateWithFormat:@"%K LIKE %@", @"sha", [parent objectForKey:@"sha"]];
			commitRequest.predicate = commitShaPredicate;
			
			NSArray *commits = [self.managedObjectContext executeFetchRequest:commitRequest error:&error];
			
			SLCommit *p;
			if(commits.count == 0){
				p = [NSEntityDescription insertNewObjectForEntityForName:@"SLCommit" inManagedObjectContext:self.managedObjectContext];
			}
			else{
				p = [commits firstObject];
			}
			p.sha = [parent objectForKey:@"sha"];
			p.url = [parent objectForKey:@"url"];
			
			p.child = head;
			[self.managedObjectContext save:&error];
		}
		//create a tree
		NSFetchRequest *treeRequest = [[NSFetchRequest alloc] initWithEntityName:@"SLTree"];
		NSPredicate *treeShaPredicate = [NSPredicate predicateWithFormat:@"%K LIKE %@", @"sha", [commitData valueForKeyPath:@"tree.sha"]];
		treeRequest.predicate = treeShaPredicate;
		NSError *error;
		NSArray *trees = [self.managedObjectContext executeFetchRequest:treeRequest error:&error];
		
		SLTree *tree;
		if(trees.count == 0){
			tree = [NSEntityDescription insertNewObjectForEntityForName:@"SLTree" inManagedObjectContext:self.managedObjectContext];
		}
		else{
			tree = [trees firstObject];
		}
		tree.url = [commitData valueForKeyPath:@"tree.url"];
		tree.sha = [commitData valueForKeyPath:@"tree.sha"];
		
		tree.commit = head;
		
		[self.managedObjectContext save:&error];
		
		//go and get that tree
		
		
		NSString *treeGetString = [NSString stringWithFormat:@"/repos/%@/%@/git/trees/%@",username,siteName,tree.sha];
		[manager GET:treeGetString parameters:@{@"access_token" : token} success:treeSuccessBlock failure:treeFailBlock];
	}
};

void (^commitFailBlock)(NSURLSessionDataTask *, NSError *) = ^void (NSURLSessionDataTask *task, NSError *error){
	NSLog(@"%@", error);
};

NSString *commitGetString = [NSString stringWithFormat:@"/repos/%@/%@/git/commits/%@", username, siteName, head.sha];

[manager GET:commitGetString parameters:@{@"access_token": token} success:commitSuccessBlock failure:commitFailBlock];
{% endhighlight %}