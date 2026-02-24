# #399 - multiple bugs in maximum_weighted_matching() [Closed]

> Username: jorisvr  
> Created at: 2024-12-05 18:53:02 UTC  
> Updated at: 2025-02-17 19:29:15 UTC  
> Closed at: 2025-02-17 19:29:13 UTC  
> Url: https://github.com/boostorg/graph/issues/399  

`maximum_weighted_matching()` gives incorrect answers for some graphs.  
And the function reports failed assertions for some other graphs.  
And the function segfaults for some other graphs as previously reported #199 .  
And the function fails to terminate for some other graphs as previously reported #223 .  
  
These issues are not rare. The failure rate appears to scale with size and density of the graph. I find that random graphs of 1000 vertices and 10000 edges with integer weights, cause the algorithm to fail for more than 50% of the graphs.  
  
The algorithm is also fairly slow for graphs of 1000 or more vertices.  
  
To clearly establish that these problems exist, I prepared a new testsuite for `maximum_weighted_matching()`. It contains a bunch of general test cases that are passing, as well as a bunch of test cases that specifically trigger each of the 4 types of failures in the algorithm. I will attach a pull request to this issue with the new tests.  
  
A possible way to solve these issues would be to replace the function with a new implementation based on code that I have been working on here: https://git.jorisvr.nl/joris/maximum-weight-matching  
  
That code would be faster, running in O(n * m * log n) compared to O(n^3) for the current algorithm. It is also more complicated. It needs a specific type of priority queue to achieve the complexity bound. Obviously this code does not fit in BGL in its current form. It would have to be rewritten to fit the BGL API and Boost coding conventions. I'd like to try that. But before I spend a lot of time on it, I would like to know whether there is any interest here in a replacing the algorithm. I understand of course that the new code would have to be reviewed and judged on its merits etc. But I'm not going to waste time on it if it is clear up front that there is no interest in this type of rework.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2024-12-09 21:34:44 UTC  
> Url: https://github.com/boostorg/graph/issues/399#issuecomment-2529570963  

I'm very interested.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2024-12-09 21:46:30 UTC  
> Url: https://github.com/boostorg/graph/issues/399#issuecomment-2529591313  

As you've discovered from looking through the issues, there are multiple problems with this algorithm, so it would be a top priority for me to see it fixed.   
Performance improvement would be icing on the cake.   
A complicated algorithm is no drama so long as there are plenty of tests.

---

## Comment 3

> Username: jorisvr  
> Created at: 2024-12-10 07:51:25 UTC  
> Url: https://github.com/boostorg/graph/issues/399#issuecomment-2530705891  

Ok great. I need some time to work on the code, then I will link a pull request here.

---

## Comment 4

> Username: jorisvr  
> Created at: 2025-01-13 21:55:18 UTC  
> Url: https://github.com/boostorg/graph/issues/399#issuecomment-2588301810  

Ok. I pushed a new implementation to the existing PR #400 .  
I think it is ready. Any feedback is very welcome.

---

## Comment 5

> Username: jeremy-murphy  
> Created at: 2025-02-15 10:20:56 UTC  
> Url: https://github.com/boostorg/graph/issues/399#issuecomment-2660857090  

I've asked the people that opened the original bugs to confirm it is fixed for them, but you can close this one whenever you're ready.

---

## Comment 6

> Username: jorisvr  
> Created at: 2025-02-17 19:29:13 UTC  
> Url: https://github.com/boostorg/graph/issues/399#issuecomment-2663938201  

Confirmed that I no longer see issues after merging #400.
