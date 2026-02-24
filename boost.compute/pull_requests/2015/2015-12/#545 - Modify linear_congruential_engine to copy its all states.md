# #545 Modify linear_congruential_engine to copy its all states. [Merged]

> Username: okdshin  
> Created at: 2015-12-27 05:33:29 UTC  
> Updated at: 2015-12-28 20:29:03 UTC  
> Merged at: 2015-12-28 20:28:58 UTC  
> Closed at: 2015-12-28 20:28:58 UTC  
> Url: https://github.com/boostorg/compute/pull/545  

The copy ctor and the assign op of compute::linear_congruential_engine does not copy its all states. So copied engine causes "Invalid Program" error.  
This modification solves this problem.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-12-27 21:23:01 UTC  
> Url: https://github.com/boostorg/compute/pull/545#issuecomment-167442361  

Thanks for the fix! Works for me :+1:   
  
IMO when fixing it's good to make 2 commits - add tests in the 1st commit and fix in the 2nd. That way it's easy to check (using just git) that it didn't work correctly and now it works fine.

---

## Comment 2

> Username: okdshin  
> Created_at: 2015-12-28 06:41:23 UTC  
> Url: https://github.com/boostorg/compute/pull/545#issuecomment-167494898  

Thanks for your advice, haahh. I fixed so :+1:

---

## Comment 3

> Username: kylelutz  
> Created_at: 2015-12-28 20:29:03 UTC  
> Url: https://github.com/boostorg/compute/pull/545#issuecomment-167647598  

Merged! Thanks!

---
