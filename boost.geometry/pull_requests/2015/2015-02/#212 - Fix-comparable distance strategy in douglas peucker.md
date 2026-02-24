# #212 Fix/comparable distance strategy in douglas peucker [Merged]

> Username: mkaravel  
> Created at: 2015-02-05 15:29:30 UTC  
> Updated at: 2015-02-09 18:48:56 UTC  
> Merged at: 2015-02-09 16:08:46 UTC  
> Closed at: 2015-02-09 16:08:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/212  

In this PR I modify the Douglas-Peucker simplify strategy to use internally comparable point-segment distances.  
I have also added a simple unit test for this strategy.

---
