# #82 Add rank spanning branchings routine for possible inclusion in library. [Open]

> Username: mbradle  
> Created at: 2016-11-06 13:24:25 UTC  
> Updated at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82  

Hello,  
  
I am opening a new version of this pull request ([previous version](https://github.com/boostorg/graph/pull/52)).  I've squashed all the development commits into a single commit (but have included the development history in the commit message).  
  
The documentation file (rank_spanning_branchings.html) describes the added code (rank_spanning_branchings.hpp).  I've included two examples (rank-branchings1.cpp and rank-branchings2.cpp), the second of which reads an input file (branching_input.txt).  I've also included a test routine (rank_branchings_test.cpp).  I added the examples and the test to the corresponding Jam files.  These have small conflicts with the development branch that I thought it might be better for the maintainers to resolve.  
  
Thanks for your consideration, and best wishes.  
  
Brad Meyer

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 18:14:26 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-417748595  

I'm helping out with the PR backlog.  Looks like you have feature additions, new documentation, no test, and new examples will be needed.  Please add tests for your added features.  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: mbradle  
> Created_at: 2018-09-02 20:21:05 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-417957159  

Thanks for the update.  There are two examples included in the pull request:  
  
example/rank-branchings1.cpp  
  
and  
  
example/rank-branchings2.cpp  
  
Once compiled, they are executed as:  
  
./rank-branchings1  
  
and (for example)  
  
./rank-branchings2 branching_input.txt -10  
  
I've included the compilation of these in the example/Jamfile.v2.  
  
There is also a test:  
  
test/rank_branchings_test.cpp  
  
Once compiled, this can be executed as (for example)  
  
./rank_branchings_test 4 10  
  
I've included it in the test/Jamfile.v2 file.  
  
I notice above that two Travis builds failed on my complex graph for clang on linux.  I can't find anything wrong with my file and have succeeded compiling and running the test code with clang-4.0 on a linux computer, so I wonder if there is an issue with the compiler on the test machine.  Nevertheless, I suppose this needs to be addressed.  
  
Best wishes.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-09-02 21:30:07 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-417961186  

I'm in the process of dealing with some other build and test failures.  When I'm done with those, I'll have you rebase your changes on upstream and that may fix your problems.  Thank you for following up!

---

## Comment 4

> Username: anadon  
> Created_at: 2018-10-15 19:53:39 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-429990410  

Can you rebase your PR?

---

## Comment 5

> Username: mbradle  
> Created_at: 2018-10-18 18:40:09 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-431117896  

I've squashed and reworded commits.  I've rebased to boostorg:develop.  I'm still seeing a couple of travis-ci failures that I can't reproduce on my end.  Thanks, and best wishes.

---

## Comment 6

> Username: anadon  
> Created_at: 2018-10-18 19:12:30 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-431127994  

You test around line 820 has a template faux pas, and is causing timeout errors.  Better, but you still need to put in just a little more work.  You're close!

---

## Comment 7

> Username: anadon  
> Created_at: 2018-10-30 22:55:36 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-434499689  

I'll get to a proper review when I can.  This week got really busy for me, sorry!

---

## Comment 8

> Username: anadon  
> Created_at: 2018-11-01 14:12:40 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-435052839  

@mbradle It appears that you're still not rebased on boostorg/graph's devel branch.  Please do so.

---

## Comment 9

> Username: anadon  
> Created_at: 2018-11-05 21:01:31 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-436033206  

@mbradle Looking more closely now.  It looks like you've done your job and everything does appear clear.  I'll need to take more time to look through though.  Please be patient while I catch up!

---

## Comment 10

> Username: mbradle  
> Created_at: 2018-11-08 02:01:04 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-436846198  

Sorry, I rebased again before I saw your last message.  I hope that's ok.  Best wishes.

---

## Review 11 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-11-08 11:00:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/82#pullrequestreview-172903365  

📁 include/boost/graph/rank_spanning_branchings.hpp

```diff
 888 |+       Edge e;
 889 |+ 
 890 |+       heap::fibonacci_heap<BranchingEntry<Edge, WeightMap, Compare> > Q;
```

> Username: jeremy-murphy  
> Created_at: 2018-11-08 11:00:49 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r231845620  

In the same tradition of other Boost.Graph algorithms, I would recommend trying to allow the user to choose what type of property map and min heap to use, and provide these choices as defaults.  
Some users will have special-case problems where different data structures are better, and in the future there might simply be a better property map, or min heap in general.  
  
You'll have to experiment to find the best way to do it, but in general by making these data structures parameters to the algorithm so that users can pass them in by reference and an overload without those parameters that chooses the defaults. It will require some experimentation and might have to be all or nothing.  
  
The other benefit of letting the client pass in data structures by reference is that the final result of computation is in them for the client to query afterwards if they like. (Think, for example, the open and closed sets in A*.)

> Username: anadon  
> Created_at: 2018-11-08 13:27:37 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r231885347  

I'm going to sit back and see how someone more experienced reviews these.  
  
So a default of a Fibonacci heap, but are there any extra concepts which must be true for a data structure to be viable that can be checked for?

> Username: mbradle  
> Created_at: 2018-11-20 20:52:12 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r235164728  

Thanks for this.  I use two priority queues--one to keep track of branchings in the ranking routine and one to keep track of edge weights in the best and next routines.  The former is just a regular priority queue (PriorityQueue concept), but the latter needs to be mergeable (MergablePriorityQueue concept).  Neither one is "visible" to the user, and the former queue will have had all its entries popped off if all branchings have been found, but I do think it makes sense to allow the user to choose, with the Fibonacci heap as the default.  I'll experiment with this (I think there are some examples in Boost Graph), and with the desirability of distinguishing between the two queues (as input--that is, pq1 for the branchings and pq2 for the graph edge weights).


---

## Review 12 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-11-08 11:07:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/82#pullrequestreview-172905611  

📁 include/boost/graph/rank_spanning_branchings.hpp

```diff
 240 |+       vertex_idx_t n = num_vertices(g);
 241 |+ 
 242 |+       disjoint_sets<Rank, Pred> W( rank, pred1 ), S( rank, pred2 );
```

> Username: jeremy-murphy  
> Created_at: 2018-11-08 11:07:21 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r231847366  

I understand the motivation for single-letter variable names like `W`, `S`, `C`, etc, but in my experience, it's better to use a descriptive name. Not everyone has the algorithm paper on hand to look up what the letters mean. And it makes the variables awfully hard to text search for in a file, such as in this review.  
`g` for graph, `u` and `v` for vertices, etc I think is fine... there is no perfect rule about it.

> Username: mbradle  
> Created_at: 2018-11-20 21:10:49 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r235170178  

Thanks, I agree with this suggestion.  Going through and making the changes will probably only reinforce your point.


---

## Review 13 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-11-08 11:08:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/82#pullrequestreview-172905939  

📁 include/boost/graph/rank_spanning_branchings.hpp

```diff
 884 |+       unordered_set<Edge> best_branching, empty_set;
 885 |+ 
 886 |+       shared_ptr< std::pair<Edge, weight_t> > p;
```

> Username: jeremy-murphy  
> Created_at: 2018-11-08 11:08:21 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r231847612  

Why `shared_ptr`? Is that really necessary?

> Username: anadon  
> Created_at: 2018-11-08 13:28:37 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r231885673  

Why avoid a shared pointer?  What is bad about them?

> Username: jeremy-murphy  
> Created_at: 2018-11-19 14:04:42 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r234628876  

Like any programming construct they are not bad intrinsically, but they are often misused when a simpler construct would suffice.

> Username: mbradle  
> Created_at: 2018-11-20 21:05:48 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r235168735  

Thanks for this.  I used pointers to check for uninitialized or not found objects, but I think maybe boost::optional is better.  For example,  
  
```diff  
-      shared_ptr< std::pair<Edge, weight_t> > p;  
+      boost::optional< std::pair<Edge, weight_t> > p;  
```  
  
The NEXT routine could then return the appropriate pair, if found.  If not found, p will be null (I guess really boost::none), as in the current implementation.  I have a branch that does that successfully.  Are there any caveats to boost::optional?


---

## Review 14 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-11-21 15:07:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/82#pullrequestreview-177284867  

📁 include/boost/graph/rank_spanning_branchings.hpp

```diff
 935 |+       {
 936 |+ 	return;
 937 |+       }
```

> Username: jeremy-murphy  
> Created_at: 2018-11-21 15:07:48 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r235423543  

Some of the indentation here seems to have gone awry?


---

## Review 15 [Commented]

> Username: jeremy-murphy  
> Created_at: 2018-11-21 15:08:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/82#pullrequestreview-177285378  

📁 include/boost/graph/rank_spanning_branchings.hpp

```diff
 473 |+                              unordered_set<Edge>& exclude_edges
 474 |+                            )
 475 |+     {
```

> Username: jeremy-murphy  
> Created_at: 2018-11-21 15:08:44 UTC  
> Updated_at: 2019-06-03 20:51:37 UTC  
> Url: https://github.com/boostorg/graph/pull/82#discussion_r235423920  

This function, `next_spanning_branching`, is very long. I recommend you break it up along the lines of major sequences, loops and/or conditions.


---

## Comment 16

> Username: anadon  
> Created_at: 2019-05-28 20:13:57 UTC  
> Url: https://github.com/boostorg/graph/pull/82#issuecomment-496671462  

I have a couple of big, no change PR's you might want to keep an eye on.

---
