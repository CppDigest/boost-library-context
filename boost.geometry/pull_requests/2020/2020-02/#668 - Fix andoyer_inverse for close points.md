# #668 Fix andoyer_inverse for close points. [Merged]

> Username: awulkiew  
> Created at: 2020-02-11 14:41:27 UTC  
> Updated at: 2020-02-13 11:14:32 UTC  
> Merged at: 2020-02-13 11:14:32 UTC  
> Closed at: 2020-02-13 11:14:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/668  

The formula treats very close points the same as antipodal points. This PR changes that.  
  
Note that cases like this are still possible even though equality of coordinates is checked at the beginning of the calculation.

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2020-02-12 11:13:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/668#pullrequestreview-357344008  

Thanks for this fix! I am ok with merging.  
  
Why not activating the tests for antipodal and very close points?   
  
Just to note here that setting azimuth and reverse_azimuth to `0` for very close points is an arbitrary choice (i.e. the error can by arbitrarily large). The important is that both values should be the same.

📁 include/boost/geometry/formulas/andoyer_inverse.hpp

```diff
 160 |+                     {
 161 |+                         result.azimuth = pi;
 162 |+                         result.reverse_azimuth = 0;
```

> Username: vissarion  
> Created_at: 2020-02-12 10:32:55 UTC  
> Updated_at: 2020-02-12 17:00:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/668#discussion_r378165667  

why not `c0`?

> Username: awulkiew  
> Created_at: 2020-02-12 13:01:33 UTC  
> Updated_at: 2020-02-12 17:00:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/668#discussion_r378235421  

Probably a mistake from the past. I'll fix it.


📁 test/formulas/inverse_cases.hpp

```diff
 606 |+         {0.26387320519564444 * 57.295779513082323, 0.84008767903244352 * 57.295779513082323},
 607 |+         {0.26387320679785603 * 57.295779513082323, 0.84008767099694759 * 57.295779513082323},
 608 |+         // TODO: Check Karney
```

> Username: vissarion  
> Created_at: 2020-02-12 10:50:25 UTC  
> Updated_at: 2020-02-12 17:00:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/668#discussion_r378174949  

on my side, `karney` formula and `geographiclib 1.50.1` returns respectively   
`0.051647212499179543,172.39749230932208,172.39749237768564,0.051647212499179543,1`  
`0.051647212499179529,172.39749230932208,172.39749237768564,0.051647212499179536,1`

---

📁 test/formulas/inverse_cases.hpp

```diff
 614 |+         {0.26387320519564444 * 57.295779513082323, 0.84008767903244352 * 57.295779513082323},
 615 |+         {(0.26387320679785603 - 3.1415926535897931) * 57.295779513082323, -0.84008767099694759 * 57.295779513082323},
 616 |+         // TODO: Check Karney
```

> Username: vissarion  
> Created_at: 2020-02-12 11:02:53 UTC  
> Updated_at: 2020-02-12 17:00:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/668#discussion_r378181500  

again on my side  
```  
--------------karney  
20003931.407432225,-1.3045327945402645e-05,-179.99998695467218,30010.522036068436,-0.99475954491334551  
--------------vincenty  
20003669.536581852,-7.591256469067261,-172.40874359800267,29487.135838230821,-0.99480541228839525  
--------------andoyer  
20018879.436128609,0,180,29935.015234617713,-0.99477272997811228  
--------------thomas  
0,0,0,0,1  
--------------geographiclib 1.50.1  
20003931.407432225,-1.3045327945402774e-05,-179.99998695467218,30010.522036068141,-0.99475954491334551  
```  
  
Are you sure you are getting that `distance` with `vincenty` ? (it is the same as `andoyer`)

> Username: awulkiew  
> Created_at: 2020-02-12 17:03:43 UTC  
> Updated_at: 2020-02-12 17:21:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/668#discussion_r378387419  

> Are you sure you are getting that distance with vincenty ?  
  
My mistake, it should be ok now.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2020-02-13 07:26:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/668#pullrequestreview-357999842  

Thanks for addressing my comments!

---
