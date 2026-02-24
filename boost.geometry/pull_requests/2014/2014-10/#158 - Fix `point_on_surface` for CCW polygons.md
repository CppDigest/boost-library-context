# #158 Fix `point_on_surface` for CCW polygons [Merged]

> Username: zerebubuth  
> Created at: 2014-10-13 03:48:09 UTC  
> Updated at: 2014-10-15 07:11:38 UTC  
> Merged at: 2014-10-14 01:58:09 UTC  
> Closed at: 2014-10-14 01:58:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/158  

I tried using `bg::reversible_view`, as recommended by @awulkiew, but wasn't able to get it to pass the tests. The patches below pass the tests, but might be better taken and re-written to use `bg::reversible_view`.  
  
Also worth noting, I had a look at the Intel and ARM documentation for their hardware prefetchers, and they both seem capable of prefetching from main memory into L2 cache regardless of the direction of the memory location access stride. The Intel docs say prefetching from L2 to L1 cache isn't possible in the reverse direction, but the lower latency and increased bandwidth between L2 and L1 should make that less of an issue than it would to main memory.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-13 10:34:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-58873956  

I was thinking about using reversible view on a Ring level, like this: https://github.com/awulkiew/geometry/commit/e18406c634fb778c7e3e855905408b959b37817f  
  
Regarding the prefetching, at some point later I realized that view could affect it only indirectly. The real cause could be the algorithms assuming CW order and a layout of data stored in e.g. Polygon. But if this was true, it'd be possible to e.g. internally store Points in CW order in CCW Polygon. So I guess it's nothing to worry about. Not to mention that the modern hardware as you wrote should probably handle this.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-10-13 10:47:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-58875007  

So before merging we should wait for the others, esspecially for Barend which is the author of the algorithm. Which solution is prefered?  
  
Btw, after merging the other fix I can't merge this one automatically so either you may merge develop and push, then it'll be possible to merge the PR on GitHub or I'll merge your changes manually and close the PR.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-10-13 21:11:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-58955483  

I'm OK with merging this. If this "kludge" (would call it factor) fixes the bug, and tests are provided, it is efficient and does not need reversible_view than (which should basically also solve the problem... but did not try)

---

## Comment 4

> Username: awulkiew  
> Created_at: 2014-10-13 22:54:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-58966841  

FYI, in my fork (https://github.com/awulkiew/geometry/tree/fix/point_on_surface), branch fix/point_on_surface, there are also the tests so if you want to try it yourself you may just checkout the whole branch.  
  
@zerebubuth would you like to do this PR automatically mergeable by yourself or should I do it?

---

## Comment 5

> Username: zerebubuth  
> Created_at: 2014-10-14 01:09:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-58977245  

I'm not sure. If you think that the way it's done in your branch is a better way to do it, I'm happy if you want to merge that instead. I'm equally happy to `s/kludge/factor/` and fix the merge conflicts with #157 - but this would create conflicts with your branch when you come to merge it. I'm just happy that it gets fixed :smile:

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-10-14 01:58:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-58980436  

I'm just sharing knowledge in case you were interested in other approach. Views are used in various algorithms around the library so it's good to know them in case you wanted to contribute more. Anyway, I'm merging.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-10-15 07:11:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/158#issuecomment-59166328  

Hi all.  
I see a problem with this PR (should have spotted that earlier): PRs should be created with respect to the develop branch rather than the master branch. Then once the PR is accepted, it gets merged into develop, and then develop is merged to master when appropriate (usually around the next release time).

---
