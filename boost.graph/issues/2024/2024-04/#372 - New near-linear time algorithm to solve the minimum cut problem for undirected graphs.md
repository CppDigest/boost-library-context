# #372 - New near-linear time algorithm to solve the minimum cut problem for undirected graphs [Open]

> Username: jeremy-murphy  
> Created at: 2024-04-30 01:42:20 UTC  
> Updated at: 2024-07-09 07:55:15 UTC  
> Url: https://github.com/boostorg/graph/issues/372  

Google Research blog post:  
https://research.google/blog/solving-the-minimum-cut-problem-for-undirected-graphs/  
  
The actual paper:  
https://epubs.siam.org/doi/10.1137/1.9781611977912.111  
  
This would presumably be a considerable improvement over our existing Stoer Wagner algorithm.

---

## Comment 1

> Username: fringewidth  
> Created at: 2024-06-28 17:25:54 UTC  
> Updated at: 2024-06-29 07:44:53 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2197365770  

Hello, I would love to work on this. I'm not sure how the contributing protocol is here. Do I get assigned the issue or should I open a pull request when I'm ready?

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2024-07-07 06:32:52 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2212341516  

Hi @fringewidth ! Welcome to Boost.Graph. If you're new to the library, it's worth looking at some recently merged pull requests to get an idea of what is expected, for lack of us having a FAQ or guide.   
  
I'll assign the ticket to you now and if you change your mind about working on it, just let me know. Assigning tickets is not strictly required and it's not strictly followed. Anyone is actually allowed to work on anything.

---

## Comment 3

> Username: fringewidth  
> Created at: 2024-07-07 08:07:56 UTC  
> Updated at: 2024-07-07 08:12:23 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2212363592  

Hey @jeremy-murphy,  
  
I was going through the paper since I left the previous comment, and noticed that the method doesn't always give the exact answer. They transform the graph into a smaller variant by removing certain edges (sparsification) to preserve the minimum cut, similar to Karger's original algorithm. However, each sparsification introduces a small probability that the minimum cut is lost. To quote the authors:  
  
> we provide a structural theorem that there exists a sparse clustering, i.e., a partition of the vertices into set such that there are few edges between the sets, that preserves minimum cuts in weighted graphs with 1/ polylog(n) multiplicative error.  
  
And elsewhere,  
  
> We address these problems by using a modified version of the techniques from [KT19, HRW20] which runs faster and is better in that the uncrossing of minimum cuts only introduces an error of (1+ϵ) at each level where ϵ = 1/ polylog(n). Thus, a subset of the edges (sampled deterministically as sketched above) that approximates the cuts in the resulting decomposition yields an accurate representation of all minimum cuts.  
  
In other words, it seems the algorithm is deterministic but not exact. I'm unsure if such an algorithm has a place in Boost.Graph, and if it does, I'm not sure how to start writing unit tests for it. Could you provide some guidance?

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2024-07-08 01:28:10 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2212747975  

Ah, interesting, I didn't realize that it was an approximation algorithm. That's fine, as getting a 99% minimum cut quickly is often perfectly good.  
Testing the correctness does sound a little tricky, but fundamentally the test would be that it is getting within the stated error of an exact answer, which I presume our existing algorithm provides?   
For unit testing, you could decompose the algorithm into parts that are amenable to unit tests, such as (I'm guessing) the sparsification logic.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2024-07-08 04:50:35 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2213021461  

I just realized that the sparsification step is probabilistic, so unit tests will only be able to test that the result has certain properties, etc.

---

## Comment 6

> Username: jeremy-murphy  
> Created at: 2024-07-08 22:30:30 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2215459345  

Ok, now things are coming back to me about testing algorithms that use randomization.   
Basically, the RNG will be a parameter to the algorithm for two reasons: i) these things are always improving, and ii) we can pass a non-random number generator for testing.  
So tests will be deterministic.

---

## Comment 7

> Username: fringewidth  
> Created at: 2024-07-09 03:38:08 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2216361154  

That works! I think I can start working on it. We don't have to worry about non-determinism, as the algorithm is deterministic. (Ironically, this poses a different issue, since we can't just run the algorithm multiple times with the same input to reduce error probability.) Regardless, I believe I can gain a better understanding by digging deeper into the method.  
  
There's a lot of reading I need to do before writing code, so I'll keep you updated with any new insights on this forum. Continuous feedback will maximize the chances of this PR being merged (I hope).

---

## Comment 8

> Username: jeremy-murphy  
> Created at: 2024-07-09 07:55:14 UTC  
> Url: https://github.com/boostorg/graph/issues/372#issuecomment-2216865131  

You're absolutely right -- teamwork is the key. Don't go up the mountain for forty nights and come back with an algorithm on stone tablets.  :) I recommend some amount of test-driven development: getting some failing tests early will help elucidate the top-down design and set you on the right path.  
  
For example, this sparsification logic sounds like a legitimate algorithm in its own right that could be reused by other algorithms.  
  
We can still fuzz the algorithm in its natural, probabilistic mode, but that wouldn't form part of the automated unit tests.
