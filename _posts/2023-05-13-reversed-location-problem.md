---
layout: post
title:  "Lost in the forest - A reversed location problem with disks and points on the plane"
date:   2023-05-13 08:00:00 -0500
categories: computationaly geometry
mathjax: true
--- 

Shawn got lost in the wood. 
He was visiting a friend – who is living like a hermit – and went off-track. 
Now he doesn’t know where he is. GPS shows that he’s in the middle of nowhere, 
and the cellular service only has one bar so he can’t make a call. 
The best Shawn could do right now is tell his friend where he is by sending a text message. 
Luckily, the friend owns the land, and he marks every tree with a unique number because 
this friend is a bit obsessive when it comes to things he owns.

Our guy moves to the nearest tree and sends its number to the friend. 
Now all he can do is wait. 
But Shawn is easily bored so he started to make up related geometry problems to solve in the meantime. 
He started thinking about how the trees can be viewed as disks from above, 
and at any point in the forest he can quickly identify all of the trees he sees by just taking a quick glance:

![fig1](/static/blog-posts/reversed-location-problem/fig1.png)

Each disk in this illustration represents a tree (when you look at them from above). 
Shawn is the point in the middle. At this location, he can see disks 4, 9, 7, 8, 15, …
Then Shawn wonders: how efficiently can a computer do that?

Given $$n$$ disjoint disks on the plane, each having a unique number, and a point $$(x, y)$$ not contained by any disk, 
how quickly can you find out which disks this point sees? 
We say a point $$u$$ sees a point $$w$$ if the line segment connecting $$u$$ and $$w$$ is not blocked by any disk:



