# #113 - Consume boostorg/disjoint_sets [Closed]

> Username: jeking3  
> Created at: 2018-08-22 13:15:48 UTC  
> Updated at: 2019-05-02 17:22:51 UTC  
> Closed at: 2019-05-02 17:22:51 UTC  
> Url: https://github.com/boostorg/graph/issues/113  

The repository disjoint_sets contains one non-detail header and it is in boost/pending.  A search of boost shows that only Boost.Graph and Boost.GraphParallel use disjoint_sets.  I would like to eliminate disjoint_sets as a separate repository to ease the burden on the CMT.  The maintainer of Boost.Container did not feel that disjoint_sets belonged there.  As such I would like to move this module into Boost.Graph and update Graph and GraphParallel appropriately.

---

## Comment 1

> Username: anadon  
> Created at: 2018-09-25 14:42:11 UTC  
> Url: https://github.com/boostorg/graph/issues/113#issuecomment-424371736  

I'd be OK with this.  Could you submit a PR resolving this and your prior ticket for review?  I'm strapped on time right now so I won't be messing with this soon myself.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-09-26 14:43:45 UTC  
> Url: https://github.com/boostorg/graph/issues/113#issuecomment-424742418  

It'll happen eventually.  I know how you feel!

---

## Comment 3

> Username: jeking3  
> Created at: 2019-05-02 11:30:09 UTC  
> Url: https://github.com/boostorg/graph/issues/113#issuecomment-488638414  

https://github.com/boostorg/graph/pull/169
