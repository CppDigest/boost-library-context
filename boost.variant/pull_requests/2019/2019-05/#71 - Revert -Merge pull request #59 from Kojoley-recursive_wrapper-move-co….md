# #71 Revert "Merge pull request #59 from Kojoley/recursive_wrapper-move-co… [Merged]

> Username: apolukhin  
> Created at: 2019-05-01 18:00:29 UTC  
> Updated at: 2021-09-20 18:25:05 UTC  
> Merged at: 2019-05-02 07:32:31 UTC  
> Closed at: 2019-05-02 07:32:31 UTC  
> Url: https://github.com/boostorg/variant/pull/71  

…nstructor-pointer-stealing"  
  
This reverts commit dcbfeb989231b85588a9743bd309d480cc184f38, reversing  
changes made to 055487c5bbf2b985b6b48cdf7e4d83654a90d878.  
  
Reverts adding a pointer stealing to the recursive_wrapper.

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-05-01 18:48:50 UTC  
> Updated_at: 2019-05-02 03:22:18 UTC  
> Url: https://github.com/boostorg/variant/pull/71#issuecomment-488375396  

[![Coverage Status](https://coveralls.io/builds/23139084/badge)](https://coveralls.io/builds/23139084)  
  
Coverage decreased (-0.2%) to 95.084% when pulling **b18c22a8fff95d9c6ebd757542a218fea2c30b81 on antoshkka/revert-recursive-move** into **0e122c38fe837868d3582f8e31dd1751d85eb93a on develop**.

---
