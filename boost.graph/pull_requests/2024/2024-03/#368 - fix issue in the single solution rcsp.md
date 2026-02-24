# #368 fix issue in the single solution rcsp [Merged]

> Username: andrea-cassioli-maersk  
> Created at: 2024-03-22 09:05:29 UTC  
> Updated at: 2024-04-03 22:46:39 UTC  
> Merged at: 2024-04-03 22:46:39 UTC  
> Closed at: 2024-04-03 22:46:39 UTC  
> Url: https://github.com/boostorg/graph/pull/368  

Refine the RCSP logic to address the issue  
  
https://github.com/boostorg/graph/issues/363  
  
* sort node labels in the post processing to make sure the path returned is the least cost instead of the first discovered.  
* there are no logic changes in the algorithm itself  
* there are no changes when rcsp finds all pareto dominated solutions  
* add a test to check the results of the fix  
* refine the git ignore to filter out some test and cmake artefacts

---

## Review 1 [Commented]

> Username: jeremy-murphy  
> Created_at: 2024-04-02 02:51:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/368#pullrequestreview-1972516757  

📁 test/rcsp_single_solution.cpp

```diff
 105 |+   const auto B = boost::add_vertex("A", graph);
 106 |+   const auto t = boost::add_vertex("B", graph);
 107 |+   const auto A = boost::add_vertex("t", graph);
```

> Username: jeremy-murphy  
> Created_at: 2024-04-02 02:51:39 UTC  
> Url: https://github.com/boostorg/graph/pull/368#discussion_r1547073313  

Did you mean to mislabel them? Does it affect the correctness of this test?

> Username: andrea-cassioli-maersk  
> Created_at: 2024-04-03 14:07:51 UTC  
> Updated_at: 2024-04-03 14:07:52 UTC  
> Url: https://github.com/boostorg/graph/pull/368#discussion_r1549827509  

thanks, good catch! The labels were wrong, but the graph overall correct. Fixed.


---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-04-02 02:58:04 UTC  
> Url: https://github.com/boostorg/graph/pull/368#issuecomment-2030984190  

This is a great contribution, thank you. Please check your test again based on my comment about the labels, make sure that it's passing _because_ of the new `sort` code. I.e., make sure that it fails if you comment the `sort` code out. Once you've confirmed that it's all good, I'll merge it.

---

## Comment 3

> Username: andrea-cassioli-maersk  
> Created_at: 2024-04-03 14:10:43 UTC  
> Url: https://github.com/boostorg/graph/pull/368#issuecomment-2034730658  

> This is a great contribution, thank you. Please check your test again based on my comment about the labels, make sure that it's passing _because_ of the new `sort` code. I.e., make sure that it fails if you comment the `sort` code out. Once you've confirmed that it's all good, I'll merge it.  
  
Just verified: commenting out the sorting I get:  
  
```  
====== BEGIN OUTPUT ======  
rcsp_single_solution.cpp(116): test 'solution_resource_container.cost == 5' failed in function 'int main()'  
1 error detected.  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---
