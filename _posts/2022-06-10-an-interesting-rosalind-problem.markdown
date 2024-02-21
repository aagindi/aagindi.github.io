---
layout: post
title:  "An Interesting Rosalind Problem"
date:   2022-06-10 19:02:20
categories: math code bio
---

### Introduction
I recently completed an interesting Rosalind problem, and I thought I'd discuss my thought process here. To me, [Rosalind](https://rosalind.info/about/){:target="_blank"} is a site packed with nice, bite sized problems to keep your problem solving skills sharp. I prefer it over similar sites since it's problems are laid out in a loose dependency tree, and it always relates the algorithmic problem to a practical application in Bioinformatics. My view of this site changed when I came across the problem [Reversal Distance](https://rosalind.info/problems/rear/){:target="_blank"}, though not necessarily for the worse. This comment is from almost a decade ago, and it sums up my initial reaction to seeing the problem:

> I'm sorry, but this an awful problem to drop at the end of the run of the novice problems, where the biggest problem is avoiding N^2 slow-downs. Then, BOOM! Solve this thing that's proven NP-complete! RIIIGHT. And all you can do is to hint "It's OK to be ugly"? Brute force is UGLY, but brute force is TOO DAMN slow to solve the problem.
> Even if you find the paper with the greedy search, and even if you implement that, you can still fail because the greedy search didn't find the optimal solution. My first problem set for this code failed, but it passed on the second.
> This problem is a LAND MINE. Grrrr..

Fortunately, comments like these are only visible after the problem is completed - they might spoil the fun otherwise!

The problem is not too difficult to wrap one's head around, but instead of describing it immediately, let's talk about some pretty elementary group theory (feel free to skip).

### Group Theory
If you've ever taken an abstract algebra course, you will have heard of $$ S_n $$, the permutation group. Basically, it is a set of all ways to shuffle around a list of *n* elements. For example, the permutation $$(1 2 4 5 3)$$ takes a list of 5 elements and shifts the third one into the fourth spot, the fourth one into the fifth spot, and the fifth element to the third spot. There is a lot of rich theory about $$ S_n $$, but I really only care about one super basic fact: $$ S_n $$ has inverses. In short, this means that for any permutation, there is some permutation that "undoes it."

Now, we're going to get into some thoughts straight from my own head. Someone else may have had them first, but nobody told me, so I'll just claim them as mine. Let $$ R_n $$ be the set of "reversals", by letting $$ R_n^{(i, j)} $$ be the permutation which reverses the elements between *i* and *j* inclusive (over $$ 1 \le i \lt j \le n $$). For example, let's consider $$ R_4 $$.

$$ R_4^{(1, 4)} = (4 3 2 1) $$ is the permutation which reverses the order of the elements from 1 to 4 inclusive, and $$ R_4^{(1, 3)} = (3 2 1 4) $$ clearly reverses the order of the elements from 1 to 3 inclusive.

Now, there are some natural questions to ask about this new set that we have. First, is $$ R_n \subseteq S_n $$? The answer is clearly yes, since every reversal essentially just shuffles up the elements of a list, which means they're all permutations. It is also clear that $$ S_n \not\subseteq R_n $$ since there are plenty of permutations (such as the first example given above) which are not reversals. Now, we can ask is $$ R_n $$ a group? The answer is a fairly immediate and resounding no - among other problems, it is not closed. For example, we can compose $$ R_n^{(1, 2)} \circ R_n^{(3, 4)} = (2 1 4 3) $$, which is not a reversal.

Another question - does $$ R_n $$ *generate* $$ S_n $$, in other words, can every permutation be expressed as the composition of reversals? Intuition says yes, and my first thought was to only consider "swaps", reversals of the form $$ R_n^{(i, i+1)} $$. We can construct any permutation $$(a_1 a_2 a_3 ...)$$ out of only swaps by first swapping $$a_1$$ to the first position (using as many swaps as needed), then $$a_2$$ to the second position, and so on. We rely on the invariant that on the *i<sup>th</sup>* step, the first *i* elements are in the order that we are aiming for, and they will never be swapped.

Now, this is a question I don't remember from elementary algebra, but I might just be out of school too long. Is there a decent upper bound on the number of reversals needed to generate a permutation? The answer is yes, $$n - 1$$. Proving this requires us to modify the above "swaps" algorithm as follows:

1. Start at the first position ($$ i = 1 $$), and with the identity element *e*. Say we want to generate the permutation *p* $$ \in S_n$$.
2. Let *j* be the position of the *i<sup>th</sup>* number of *p* in *e*. Now, take the *e* and apply the reversal $$ R_n^{(i, j)} $$, which will place the *i<sup>th</sup>* element of *p* into the *i<sup>th</sup>* position of *e*
3. Continue in this way for all of the elements of *p* (i.e. increment *i* until it is greater than *n*, then end)

Note that this expression always uses exactly $$n-1$$ reversals which is definitely not always optimal. Also, for the sake of not abusing notation, define that if $$i=j$$, then the reversal is the identity element. Also, note that $$ j \ge i $$ always in step 2.

Is this $$n-1$$ a tight bound? Honestly, I do not know, but it is tight for $$ n = 10 $$. The reason I know *this* is that we're given a permutation of length 10 that we are told requires 9 reversals to generate it.

### The Problem, the Solution?
The general problem is simple to describe - compute the *reversal distance* between two given permutations. The Rosalind Team defines the reversal distance $$ d_{rev}(\pi, \sigma) $$ as the minimum number of reversals required to transform $$\pi$$ into $$\sigma$$. Hopefully it will become clear why I choose to discuss generating the permutation instead of reversal distance.

Now, the angry comment makes a bit more sense - this is indeed provably an NP-hard problem. However, problems like these are approachable at small scale. The way that reversals can fold back on themselves is a hallmark of complexity (think [*strange loops*](https://www.google.com/search?q=strange+loops){:target="_blank"}), so it is pretty clear that we're going to need to perform a breadth-first search. Ok, setting up a quick breadth-first search procedure in Python allows us to compute the first given example in approximately 2 minutes, and it turns out the rest are not much faster. Unfortunately, due to the rules of Rosalind, this is too slow - we need to complete 5 searches in less than 5 minutes.

This is the point at which many people probably give up on this problem. At time of writing, 1009 people have completed this challenge, which is about an order of magnitude fewer than many other problems on the site. This is where some insight (or impressive Googling skill) is required. There are - it turns out - quite a few solutions. The first that I discovered in the solution comment section was a meet-in-the-middle modification of BFS. Another is a greedy algorithm (alluded to by the angry comment) that is not actually always correct. It turns out that my solution was the "most correct" (Rosalind listed it as the solution). The necessary insight is that the starting point does not really matter that much. Unfortunately, to explain this, we need to go back to the group theory.

Consider an optimal *reversal path* from $$A$$ to $$B$$ with reversal distance *d*:

$$ (r_1 \circ r_2 ... \circ r_d) \circ A = B $$

Recall that all group elements have inverses, so we can create (and compute) the permutation $$A^{-1}$$, and multiply both sides by it (on the right):

$$ (r_1 \circ r_2 ... \circ r_d) \circ A \circ A^{-1} = (r_1 \circ r_2 ... \circ r_d) \circ e = (r_1 \circ r_2 ... \circ r_d) = B \circ A^{-1} $$

Basically, what we find is that we can always start our search from the identity element *e* as long as we're searching for the correct permutation, which it turns out is not $$A$$ or $$B$$ but instead a sort of combination of the two. This allows us to merely pre-compute the shortest path between the identity element and all other elements. Interestingly, this is the same as computing how many reversals it takes to generate any given permutation!

After that, every subsequent example requires only the time to compute $$ B \circ A^{-1} $$ plus the time of a hash-table lookup - constant time. Ok, the pre-computation step takes approximately $$O(n!)$$, but we can do that before the 5 minute countdown even begins.

### So...

That's the problem, and the solution. As always at this juncture, I'm left with a single question - are there any open questions? There is one that jumps out a me. Is the $$n-1$$ bound tight? It didn't matter in the case of $$n=10$$ since we're provided right off the bat with an instance where the upper bound is achieved. But is 10 special? Can all permutations of some other length be generated in less than $$n-1$$ reversals? Well, I do not know. Maybe I'll try to figure it out.
