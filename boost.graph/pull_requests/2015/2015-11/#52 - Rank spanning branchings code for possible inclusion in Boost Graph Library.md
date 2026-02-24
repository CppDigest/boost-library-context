# #52 Rank spanning branchings code for possible inclusion in Boost Graph Library [Closed]

> Username: mbradle  
> Created at: 2015-11-26 14:32:32 UTC  
> Updated at: 2016-11-03 13:36:32 UTC  
> Closed at: 2016-11-03 13:36:32 UTC  
> Url: https://github.com/boostorg/graph/pull/52  

Hello,  
  
I am submitting this implementation of the Camerini et al. algorithm to rank spanning branchings in directed graphs for possible inclusion in the Boost Graph Library.  The algorithm extends the Edmonds and Chu/Liu algorithm for finding the optimum branching to find the k optimum branchings (in order).  The complexity is O(kElog(V)).  I have included two example codes (example/rank-branchings1.cpp and example/rank-branchings2.cpp) that demonstrate usage.  I have also included two test codes (test/test_int_ranking.cpp and test/test_real_ranking.cpp) that construct (by brute force) all branchings in random complete graphs and then compare their ordering to the output from rank_spanning_branchings().  The test script test/test_spanning_branchings.sh executes these test codes and can be executed as  
  
**./test_spanning_branchings 4**  
  
where 4 would be the number of vertices in a random complete graph.  Finally, I have included a documentation file (doc/rank_spanning_branchings.html).  
  
I know there was some interest in [including](http://sourceforge.net/p/edmonds-alg/mailman/message/24951212/) an implementation of Edmonds' algorithm into Boost.  I think my implementation would be a good possibility as well as an extension to find the subsequent best branchings.  In any event, thanks for your consideration, and best wishes.  
  
Brad Meyer

---

## Comment 1

> Username: Belcourt  
> Created_at: 2016-11-01 02:05:38 UTC  
> Url: https://github.com/boostorg/graph/pull/52#issuecomment-257472794  

This is a large commit, hard to review.  Can it be broken up in some way or can you rebaseline and squash it into a single commit (and jettison all of the unrelated code changes and bug fixes, i.e. move that code into separate PRs)?

---

## Comment 2

> Username: mbradle  
> Created_at: 2016-11-03 13:36:32 UTC  
> Url: https://github.com/boostorg/graph/pull/52#issuecomment-258143745  

Yes, I'm sorry about that.  I've made a lot of changes/improvements since the original version.  I will close this PR, squash to a single commit, and open a new, single PR in the next day or two.  Thanks, and best wishes.

---
