# #29 New search algorithms for Boost.Algorithm [Open]

> Username: zamazan4ik  
> Created at: 2016-10-23 20:06:17 UTC  
> Updated at: 2016-11-01 22:33:43 UTC  
> Url: https://github.com/boostorg/algorithm/pull/29  

Now in Boost.Algorithm we have only 3 algortihms for searching: Knuth-Morris-Prata, Boyer-Moore, Boyer-Moore-Horspool.  
  
I want to add more algorithms.   
  
Please, if you know something about string search algortihms, i want to talk with you.

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2016-10-26 21:27:17 UTC  
> Url: https://github.com/boostorg/algorithm/pull/29#issuecomment-256482193  

Soon i will rewrite preBmBc - it should use unordered_map\flat_map instead of alphabet array. Also I will add object interface.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2016-11-01 22:33:43 UTC  
> Url: https://github.com/boostorg/algorithm/pull/29#issuecomment-257719372  

I think i should stop implement new algorithms. We should decide, what interface must have all of these algorithms. After that i will continue my work on searching.

---
