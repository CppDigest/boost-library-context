# #281 Added Karps Minimum Cycle Algorithm [Open]

> Username: GYuvanShankar  
> Created at: 2021-12-19 08:34:41 UTC  
> Updated at: 2022-07-20 01:05:16 UTC  
> Url: https://github.com/boostorg/graph/pull/281  

Based on issue #272.  
Implemented [Karps Minimum Cycle Algorithm](https://www.sciencedirect.com/science/article/pii/0012365X78900110).

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2022-01-04 01:44:07 UTC  
> Updated_at: 2022-01-04 01:49:55 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1004469198  

Thanks for taking the time to implement this. It looks good, but as I said earlier it will take a few rounds of feedback to get it to be consistent with existing BGL code.  
  
Please take a look at an existing similar algorithm such as [Howard's cycle ratio algorithm](https://www.boost.org/doc/libs/1_78_0/libs/graph/doc/howard_cycle_ratio.html)  and imitate what is there. There is a lot more to an algorithm than just the implementation, namely:  
- Documentation  
- Examples  
- Unit tests  
  
PS. One thing that I do not expect anyone to imitate from the existing code are named parameters.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2022-01-04 01:47:27 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1004470090  

I marked this as a draft for now in the hope that it won't trigger the CI every time you push -- I'm still learning how this CI system works. When you have completed everything then mark it ready for review again.

---

## Review 3 [Commented]

> Username: jeremy-murphy  
> Created_at: 2022-01-04 01:51:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/281#pullrequestreview-843147848  

📁 include/boost/graph/karp_minimum_mean_cycle.hpp

```diff
   9 |+ {
  10 |+     template<typename Graph>
  11 |+     double karp_minimum_mean_cycle(Graph g)
```

> Username: jeremy-murphy  
> Created_at: 2022-01-04 01:51:58 UTC  
> Url: https://github.com/boostorg/graph/pull/281#discussion_r777784679  

Although it doesn't roll off the tongue very well, the paper does call it the "minimum cycle mean".


---

## Comment 4

> Username: GYuvanShankar  
> Created_at: 2022-01-04 07:33:59 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1004583895  

Thanks for looking into this. I will start the documentation, was just waiting for someone to approve of the code.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2022-02-21 00:54:52 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1046369259  

Hi @GYuvanShankar , thanks, it's looking good! I'll start a review, in which I'll make some (or many) small comments in the code itself.

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2022-04-12 01:12:20 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1095763659  

When implementing algorithms, of any kind, it's important to be really familiar with the literature on them. In this case there is at least one paper that demonstrates a defect with Karp's algorithm and an alternative solution: https://www.cs.colostate.edu/~rmm/minCycleMean.pdf  
  
I recommend that you read widely on MCM and then come back with a discussion on which algorithm(s) you propose to implement and why. This is an interesting overview, although I have only glanced at it: https://www.researchgate.net/profile/Rajesh-Gupta-10/publication/2689582_An_Experimental_Study_of_Minimum_Mean_Cycle_Algorithms/links/546e071f0cf29806ec2e6dba/An-Experimental-Study-of-Minimum-Mean-Cycle-Algorithms.pdf

---

## Review 7 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2022-07-15 01:05:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/281#pullrequestreview-938733813  

I'm so sorry, I didn't realize that these review comments were in a "pending" state, not published.

📁 include/boost/graph/karp_minimum_cycle_mean.hpp

```diff
   2 |+ #define BOOST_GRAPH_KARP_MINIMUM_CYCLE_MEAN_HPP
   3 |+ 
   4 |+ #include <iostream>
```

> Username: jeremy-murphy  
> Created_at: 2022-04-12 00:26:04 UTC  
> Updated_at: 2022-07-15 01:05:01 UTC  
> Url: https://github.com/boostorg/graph/pull/281#discussion_r847838957  

We don't allow `<iostream>` in header files: it brings in a lot of names and static objects, etc.

---

📁 include/boost/graph/karp_minimum_cycle_mean.hpp

```diff
   9 |+ {
  10 |+     template<typename Graph>
  11 |+     double karp_minimum_cycle_mean(Graph g)
```

> Username: jeremy-murphy  
> Created_at: 2022-04-12 00:27:42 UTC  
> Updated_at: 2022-07-15 01:05:01 UTC  
> Url: https://github.com/boostorg/graph/pull/281#discussion_r847839568  

I think you'll want to take `g` by `const` reference.

---

📁 include/boost/graph/karp_minimum_cycle_mean.hpp

```diff
  16 |+         typedef typename boost::graph_traits<Graph>::out_edge_iterator out_edge_iterator;
  17 |+         BOOST_CONCEPT_ASSERT((GraphConcept< Graph >));
  18 |+ 
```

> Username: jeremy-murphy  
> Created_at: 2022-04-12 00:33:26 UTC  
> Updated_at: 2022-07-15 01:05:01 UTC  
> Url: https://github.com/boostorg/graph/pull/281#discussion_r847841522  

A comment about this file in general, we don't have automatic Clang format running (yet), so please follow the convention in the rest of the library:  
* whitespace around operators: `a + b`, `a = b`, etc.


📁 doc/karp_minimum_cycle_mean.html

```diff
  56 |+ <P>
  57 |+ This algorithm was described by Richard M. Karp in his paper
  58 |+ <A HREF="./dasdan-dac99.pdf">A characterization of the minimum cycle mean in a digraph</A></P>
```

> Username: jeremy-murphy  
> Created_at: 2022-04-12 01:04:18 UTC  
> Updated_at: 2022-07-15 01:05:01 UTC  
> Url: https://github.com/boostorg/graph/pull/281#discussion_r847852334  

Please give a proper citation of the paper.


---

## Comment 8

> Username: jesseli2002  
> Created_at: 2022-07-19 19:30:41 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1189472443  

> PS. One thing that I do not expect anyone to imitate from the existing code are named parameters.  
Why not? Are named parameters being deprecated or something?

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2022-07-20 01:05:16 UTC  
> Url: https://github.com/boostorg/graph/pull/281#issuecomment-1189694398  

> > PS. One thing that I do not expect anyone to imitate from the existing code are named parameters.  
> > Why not? Are named parameters being deprecated or something?  
  
It was a courageous and ambitious experiment at the time (20 something years ago) but it adds an awful lot of complexity and maintenance burden to the code for questionable benefit. In the long run, I would like to strip them out. I think we could achieve similar benefits, if wanted, from much simpler overloads and stronger types.

---
