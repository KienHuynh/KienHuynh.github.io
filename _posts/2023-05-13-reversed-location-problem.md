---
layout: post
title:  "Lost in the forest - A reversed location problem with disks and points on the plane"
date:   2023-05-13 08:00:00 -0500
categories: computationaly geometry
mathjax: true
image: /static/blog-posts/reversed-location-problem/fig5.png
description: Finding your location in a forest of disks.
comments: true
--- 

Shawn got lost in the wood. 
He was visiting a friend – who is living like a hermit – and went off track. 
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

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig1.png" alt="fig1" width="80%" /> 
</p>

Each disk in this illustration represents a tree (when you look at them from above). 
Shawn is the point in the middle. At this location, he can see disks 4, 9, 7, 8, 15, …
Then Shawn wonders: how efficiently can a computer do that?

| Problem 1 |
|:----------|
|Given $$n$$ disjoint disks on the plane, each having a unique number, and a point $$(x, y)$$ not contained by any disk, how quickly can you find out which disks this point sees?|

We say a point $$u$$ sees a point $$w$$ if the line segment connecting $$u$$ and $$w$$ is not blocked by any disk:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/visibility.png" alt="vis-def" width="50%" /> 
</p>

$$u$$ sees $$w$$ but not $$v$$. $$v$$ sees $$w$$ but not $$u$$. $$w$$ can see both. 
This “a sees b” relation applies to both points on the plane and points on the boundary of disks. 
Finally, we say that a point $$u$$ sees disk $$i$$ if it sees at least one point on the boundary of disk $$i$$.

The naïve way (this is starting to sound like a leetcode session, 
but I promise that not everything in this post is a leetcode problem) is to check every disk, $$i$$, 
and see if it’s blocked by some other disks. 
To do this, we look at the two tangent lines of disk $$i$$ from our location. 
Since our location and that of the disk’s is known, we can model these lines by two numbers (or an interval): 
$$(a_i, b_i)$$, which are their angles with the positive x-axis. 

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig2.png" alt="fig2" width="45%" /> 
</p>

If disk $$j$$ is closer to us then disk $$i$$ and $$(a_j, b_j)$$ intersect with $$(a_i, b_i)$$ 
then we know that disk $$j$$ is blocking disk $$i$$ by some amount. 
We can easily calculate that and reduce $$(a_i, b_i)$$. Once $$(a_i, b_i) = \emptyset$$, 
we know that disk $$i$$ is completely hidden from $$(x, y)$$. The naïve running time is $$O(n^2)$$.

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig3.png" alt="fig3" width="80%" /> 
</p>

Here, disk $$j$$ and $$k$$ are closer to $$(x, y)$$ than disk $$i$$. 
Additionally, the intervals $$(a_j, b_j)$$ and $$(a_k, b_k)$$ completely block $$(a_i, b_i)$$ from our viewpoint.

But Shawn thinks he can do better than this: there is an $$O(n\log n)$$ solution and it’s the best we can do. 
I will leave the solution for you and Shawn to find out. 
You can deduce the answer yourself with this hint: it has to do with sorting.

While waiting for the hermit to arrive, the lost guy starts to make up another problem – the loosely reversed version of the above:

| Problem 2 |
|:----------|
|You have $$n$$ disks on the plane, each with a unique number. Then, given a subset of these disks, can you find the location(s) that can see all of the disks in this subset?|

Here, the input subset is just a list of tree IDs, not a list of intervals 
(otherwise it would be fairly trivial and unsatisfying, and Shawn has to think of a new problem).

Shawn quickly noticed the trouble with this problem: the location may not be unique!
For example, if you have one single tree, then you can see it from practically anywhere. 
The solution is the entire unbounded 2D plane minus this lonely tree.
This is a problem because if Shawn tells his friend that he sees tree $$1$$, the friend will have no way to narrow Shawn down:
Shawn could be standing in the realm of infinity.

But (un)luckily for Shawn, he is stuck in the middle of the forest, completely surrounded by trees. 
Therefore, he can safely assume that: if you put a point in this forest, 
its $$360^\circ$$ vision will only ever consist of trees, trees, and more trees. 
This means that the answer to Shawn's problem will always be a bounded region – it doesn’t stretch to infinity
(In general, regions that stretch to infinity can still be dealt with algorithmically, but in practice our domain is usually bounded).

Anyway, there is a horrible way to solve this problem: we sample a lot of points in the plane. 
For each such point, compute the list of disks that it sees: some points will have a list that matches the input list, some will not. 
Eventually we will be able to obtain an approximately accurate region representing our solution. 
But this can be inefficient because we may have to sample a lot of points – which can take a long time, 
and the accuracy of the solution depends on the number of sampled points which isn't exactly appealing.

To actually solve this, Shawn observes the following: 
if you put a point $$u$$ on the boundary of any disk and compute the region that this point sees, 
then any point $$w$$ that lies inside this region must also see $$u$$:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig4.png" alt="fig4" width="80%" /> 
</p>

(This orange region that $$u$$ sees in the above picture is the **visibility region** of $$u$$)

Even though the above example only showcases one point, 
Shawn realizes he can generalize it to the entire boundary of a disk. 
Let $$V_i$$ be the regions that disks $$i$$ can “see”, 
we define this as the union of all visibility regions of all points on the boundary if this disk. 
The actual name for $$V_i$$ is the **weak visibility region** of disk $$i$$. See below for an example of such region:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig5.png" alt="fig5" width="80%" /> 
</p>

The orange-shaded portion is the weak visibility region of disk $$i$$. 
If we put any point $$w$$ in this region, it means that disk $$i$$ will “see” $$w$$ and vice versa.

If we can compute such a region, then this is a better way to model the problem than the sampling method. 
Say, we compute such regions for every disk. Then whenever we put a point $$(x, y)$$ on the plane as a query point, 
we can check to which regions $$(x, y)$$ belongs and answer which disks it sees. 
It works the other way as well: when we are given a list of disks and we want to find out a region that sees all of them, 
we can simply take the intersection of all $$V_i$$ (this intersection could also be empty if the disk list is invalid).
The main question is how fast can this intersection computation be?

Let $$m$$ be the number of disks in the input list, the running time for a query here is around $$O(m(n + I)\log n)$$
where $$I$$ could be anything between a constant and $$O(n^4)$$.
To understand what $$I$$ is, first we need to consider the complexity of the intersection beween two closed curves 
each having $$O(n)$$ complexity (let's assume that the weak visibility regions of the disks have linear complexity for now).
Its computation can take up to $$O((n + J) \log n)$$ time where $$J$$ is the number of intersection points.
Since we have $$m$$ closed curves to perform intersection on,
the total running time would be $$O(m(n + I)\log n$$ and $$I$$ is the largest
number of intersection points we can possibly get.

But where does the $$O(n^4)$$ come from? And how do we compute weak visibility regions of the disks?
The answer to both of these involves lots and lots of bitangent lines. 
As a refresher, a bitangent line is a line tangent to at least 2 different disks.
Below are four bitangent lines of two disks:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/4bitangents.png" alt="4bitangents" width="60%" /> 
</p>

Generally, there are at most four bitangents between any two disks (unless they intersect, 
but since we are working with trees let’s assume that at worst, they will only touch each other). 
To see how bitagents are relevant here, Shawn cooks up an example:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig7.png" alt="fig7" width="50%" /> 
</p>

Consider the set of uninterrupted segments, i.e. segments not passing through another disk, that connects a point on the boundary of disk $$i$$ to a point on that of $$j$$. 
Instead of computing all such segments, which is a tall order, we can just check the bitangents that hit disk $$i$$ and $$j$$ instead:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig8.png" alt="fig8" width="50%" /> 
</p>

The orange region with blue outline above is the region of points that see both $$i$$ and $$j$$. 
Notice that to construct this region, we only need to compute a finite amount of bitangents. 
Intuitively, this is the region we get by "wiggling" an uninterrupted segment connecting disk 
$$i$$ and $$j$$ around as much as we can:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig9.png" alt="fig9" width="50%" /> 
</p>

Depending on the arrangement of the disks, we may need to “wiggle” more than one segment between disks $$i$$ and $$j$$:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig10.png" alt="fig10" width="80%" /> 
</p>

But even here, we still only need to compute a **finite amount of bitangents**.

As you can see, to compute the set of points that see both disk $$i$$ and $$j$$, 
we find the bitangents hitting both of them. 
We can repeat this process to for all disks $$j\neq i$$ to find points that see both $$i$$ and $$j$$. 
Finally, the union of all of these regions will be the region of points that see disk $$i$$, 
which is also its weak visibility region:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig6.png" alt="fig6" width="55%" /> 
<img src="/static/blog-posts/reversed-location-problem/fig5.png" alt="fig5" width="55%" /> 
</p>

The process of computing each such region is not difficult but it is a bit involved.
We can deal with it by treating the whole thing as a graph: 
the intersections of the bitagents are vertices and the segment connecting them are edges.
A simple walk around the outer boundary of this (planar) graph will give us the weak visibility region.

In practice, we can first compute all uninterrupted bitangents. 
The set of bitangents, along with the disks’ boundaries, will split the plane into **faces**:

<p align="center">
<img src="/static/blog-posts/reversed-location-problem/fig11.png" alt="fig11" width="80%" /> 
</p>
and we can use these faces to create weak visibility regions for every disks.
(This looks more intimidating than it should be, and I probably missed a few bitangents.
In the worst case, we can actually get up to 64 bitangents with just four disks).

Now we can finally get the answer to why $$I$$ can be at most $$O(n^4)$$.
Since the weak visibility regions of $$m$$ disks are made up of these small faces, the intersection is also a subset of these faces.
Because we have at most $$O(n^2)$$ bitangents, [in the worst case we can have $$O(n^4)$$ faces](https://en.wikipedia.org/wiki/Arrangement_of_lines).
The total complexity of all of these faces is also $$O(n^4)$$, which is the worst $$I$$ can be.

One neat thing about the not-so-neat-looking thing above is that, 
if you put a point in a face and move it within that face (not on the boundary), 
**the set of disks that this point sees will not change**. 
In the above example, all points in the orange face will only see disk 1, 2 and 3. 
For the blue face, all points within it will only see disk 1 and 4. 
Due to this property, this structure also helps us improve the query time for **Problem 1** as well.
This subdivision of the planes into faces by the lines and disks is called a “visibility arrangement”.

Going back to **Problem 1**, we can perform quicker queries by first storing all faces in the above arrangement.
When we have a new query point, we will just check which face contains it,
which can be done in $$O(\log n)$$ time using some specific [data structure](https://en.wikipedia.org/wiki/Point_location).
Reporting the list of disks that the face sees will take $$O(s)$$ where $$s$$ is the number of disks the face sees.

As we are thinking about algorithms and data structures, the hermit finally finds Shawn.
They greet each other and head to their supposed hangout place.
Maybe they will think of another geometry problem for us to write about.