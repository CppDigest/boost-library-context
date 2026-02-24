# #867 - qbegin(idx::nearest(pt, 100)) fast but qbegin(idx::nearest(pt, tree_size)) really slow [Closed]

> Username: karme  
> Created at: 2021-06-14 13:39:18 UTC  
> Updated at: 2021-09-22 18:26:10 UTC  
> Closed at: 2021-09-22 18:26:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/867  

If I do a ```t->qbegin(idx::nearest(pt, 100))``` it is really fast (<1ms) but  
if I do a ```t->qbegin(idx::nearest(pt, tree_size))``` with tree_size=37133 it is really  
slow (~500ms)  
  
s.a. boost users mailing list thread:  
"geometry: knn search of point to many linestrings?"  
https://lists.boost.org/boost-users/2021/05/90908.php  
especially:  
https://lists.boost.org/boost-users/2021/06/90929.php

---

## Comment 1

> Username: karme  
> Created at: 2021-06-15 15:17:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-861589456  

trying to reproduce with a simple test:  
```  
$ ./get-polylines.scm > polylines  
$ ./test3.cpp < polylines  
linestrings=29973  
t.size()=29973  
2850usec  
14524956usec  
```

---

## Comment 2

> Username: karme  
> Created at: 2021-06-15 15:24:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-861595191  

the test files:  
[rtree.zip](https://github.com/boostorg/geometry/files/6656799/rtree.zip)

---

## Comment 3

> Username: awulkiew  
> Created at: 2021-06-15 21:07:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-861831742  

Thanks for the report! I'll look into this next week.

---

## Comment 4

> Username: karme  
> Created at: 2021-07-07 06:00:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-875308444  

@awulkiew can you reproduce the problem?

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-07-07 11:02:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-875509977  

@karme Sorry that it takes so long. Yes, I was able to reproduce it with msvc. The time depends on `k` passed into `nearest()`.  
```  
linestrings=29973  
t.size()=29973  
k=100:   0.0001562sec  
k=299:   0.001073sec  
k=2997:  0.0636702sec  
k=29973: 0.47566sec  
```  
  
There is also another issue, i.e. `nearest()` takes `k` as `unsigned int` which may be too small to hold value returned by `rtree.size()`.

---

## Comment 6

> Username: awulkiew  
> Created at: 2021-07-07 21:05:04 UTC  
> Updated at: 2021-07-07 21:08:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-875932171  

@karme I prepared a fix for this issue. The times decreased to:  
```  
k=100:   1.45e-05sec  
k=299:   8.1e-06sec  
k=2997:  6.7e-06sec  
k=29973: 5.5e-06sec  
```  
so they're too short to be reliably compared for this dataset.  
  
Besides the two issues I mentioned above I found the third one. Details are in the PR. If you want to test the fix you can pull this branch: https://github.com/awulkiew/geometry/tree/fix/rtree_nearest

---

## Comment 7

> Username: karme  
> Created at: 2021-07-13 14:10:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-879122291  

looks very good so far - thanks for looking into this!

---

## Comment 8

> Username: karme  
> Created at: 2021-07-14 15:57:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-880013486  

in a bigger test (388521289 linestrings) the performance isn't good  
enough:  
  
qbegin(idx::nearest(pt, 100)) 398usec  
qbegin(idx::nearest(pt, 388521289)) 11265usec  
qbegin(idx::nearest(pt, 100)) 545usec  
qbegin(idx::nearest(pt, 388521289)) 16922usec  
qbegin(idx::nearest(pt, 100)) 468usec  
qbegin(idx::nearest(pt, 388521289)) 17506usec  
qbegin(idx::nearest(pt, 100)) 496usec  
qbegin(idx::nearest(pt, 388521289)) 20017usec  
qbegin(idx::nearest(pt, 100)) 491usec  
qbegin(idx::nearest(pt, 388521289)) 20038usec  
qbegin(idx::nearest(pt, 100)) 557usec  
qbegin(idx::nearest(pt, 388521289)) 16512usec  
qbegin(idx::nearest(pt, 100)) 448usec  
qbegin(idx::nearest(pt, 388521289)) 9190usec  
qbegin(idx::nearest(pt, 100)) 421usec  
qbegin(idx::nearest(pt, 388521289)) 8921usec  
qbegin(idx::nearest(pt, 100)) 417usec  
qbegin(idx::nearest(pt, 388521289)) 10394usec  
qbegin(idx::nearest(pt, 100)) 412usec  
qbegin(idx::nearest(pt, 388521289)) 10943usec  
qbegin(idx::nearest(pt, 100)) 447usec  
qbegin(idx::nearest(pt, 388521289)) 16530usec  
qbegin(idx::nearest(pt, 100)) 458usec  
qbegin(idx::nearest(pt, 388521289)) 15806usec  
qbegin(idx::nearest(pt, 100)) 467usec  
qbegin(idx::nearest(pt, 388521289)) 13388usec  
qbegin(idx::nearest(pt, 100)) 452usec  
qbegin(idx::nearest(pt, 388521289)) 13283usec  
qbegin(idx::nearest(pt, 100)) 529usec  
qbegin(idx::nearest(pt, 388521289)) 16514usec  
qbegin(idx::nearest(pt, 100)) 368usec  
qbegin(idx::nearest(pt, 388521289)) 5055usec  
qbegin(idx::nearest(pt, 100)) 322usec  
qbegin(idx::nearest(pt, 388521289)) 4607usec  
qbegin(idx::nearest(pt, 100)) 458usec  
qbegin(idx::nearest(pt, 388521289)) 18208usec  
qbegin(idx::nearest(pt, 100)) 415usec  
qbegin(idx::nearest(pt, 388521289)) 11516usec  
qbegin(idx::nearest(pt, 100)) 547usec  
qbegin(idx::nearest(pt, 388521289)) 10569usec  
qbegin(idx::nearest(pt, 100)) 412usec  
qbegin(idx::nearest(pt, 388521289)) 10182usec  
qbegin(idx::nearest(pt, 100)) 420usec  
qbegin(idx::nearest(pt, 388521289)) 10194usec  
qbegin(idx::nearest(pt, 100)) 403usec  
qbegin(idx::nearest(pt, 388521289)) 6656usec  
qbegin(idx::nearest(pt, 100)) 362usec  
qbegin(idx::nearest(pt, 388521289)) 8143usec  
qbegin(idx::nearest(pt, 100)) 490usec  
qbegin(idx::nearest(pt, 388521289)) 14744usec

---

## Comment 9

> Username: awulkiew  
> Created at: 2021-07-14 22:24:35 UTC  
> Updated at: 2021-07-14 22:32:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-880249715  

You're testing with the fix right?  
  
I tried to reproduce it with some a synthetic test (uniform distribution with overlap):  
```  
for (size_t i = 0; i < 12000; i++) {  
      for (size_t j = 0; j < 12000; j++) {  
          t.insert(value_type(B{ {i+0.0, j + 0.0}, {i+2.0, j+2.0} }, linestrings));  
          ++linestrings;  
      }  
  }  
```  
but was unable to:  
```  
linestrings=144000000  
t.size()=144000000  
k=100:   1.81e-05sec  
k=1440000:   6.8e-06sec  
k=14400000:  6.8e-06sec  
k=144000000: 5.2e-06sec  
```  
The internals of the iterator looks good too. Regardless of `k` passed into `nearest()` the number of nodes traversed and neighbors gathered in `qbegin()` are the same:  
```  
k=100:  
heap size: 69  
neighbors size: 18  
k=1440000:  
heap size: 69  
neighbors size: 18  
k=14400000:  
heap size: 69  
neighbors size: 18  
k=144000000:  
heap size: 69  
neighbors size: 18  
```  
  
Is there something specific about the data you're testing against?  
How does the vicinity of the point looks like?  
E.g. is it possible that there is a big number of linestring bounding boxes intersecting the point (greater than 100)?  
Could you prepare some data allowing me to reproduce it (not more than 100M linestrings if possible)?  
  
Btw, here is the branch where `qbegin()` ends traversing the tree:  
https://github.com/awulkiew/geometry/blob/fix/rtree_nearest/include/boost/geometry/index/detail/rtree/visitors/distance_query.hpp#L401  
You could put here e.g.  
```  
std::cout << "heap size: " << internal_heap.size() << std::endl;  
std::cout << "neighbors size: " << neighbors.size() << std::endl;  
```  
to see whether or not there is any difference.

---

## Comment 10

> Username: karme  
> Created at: 2021-07-16 07:41:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881244979  

i will need some time to answer the other questions / prepare a "small" test  
  
but a quick test of the last one:  
> You could put here e.g.  
> std::cout << "heap size: " << internal_heap.size() << std::endl;  
> std::cout << "neighbors size: " << neighbors.size() << std::endl;  
> to see whether or not there is any difference.  
  
shows:  
heap size: 1030  
neighbors size: 100  
qbegin(idx::nearest(pt, 100)) 2055usec  
heap size: 11066  
neighbors size: 3452  
qbegin(idx::nearest(pt, 388521289)) 6406usec

---

## Comment 11

> Username: awulkiew  
> Created at: 2021-07-16 09:44:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881319239  

Right, something like this may happen if there is big number of nodes and values covering the point so the distance to all of them is 0. I think I have an idea what I could do about this.

---

## Comment 12

> Username: karme  
> Created at: 2021-07-16 09:53:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881324959  

> Is there something specific about the data you're testing against?  
  
it is openstreetmap street data  
  
> How does the vicinity of the point looks like?  
> E.g. is it possible that there is a big number of linestring bounding boxes intersecting the point (greater than 100)?  
  
I use:  
  
```  
    {  
	std::vector<value_t> results;  
	t->query(idx::intersects(pt), std::back_inserter(results));  
	cerr << "intersections " << results.size() << endl;  
    }  
```  
to get the number of intersections.  
  
=> typically there are only 1-2 bounding boxes intersecting the point

---

## Comment 13

> Username: karme  
> Created at: 2021-07-16 10:01:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881329826  

a really bad example is:  
```  
(pick 858855 6502943 1 10000 hiking)  
heap size: 2197  
neighbors size: 100  
qbegin(idx::nearest(pt, 100)) 1518usec  
intersections 1  
heap size: 13205  
neighbors size: 6088  
qbegin(idx::nearest(pt, 388521289)) 46112usec  
```  
  
gosh> (mercator⁻¹ '(858855 6502943 0))  
(7.715225733424715 50.32609813283437 0)  
https://www.openstreetmap.org/#map=19/50.32609813283437/7.715225733424715

---

## Comment 14

> Username: awulkiew  
> Created at: 2021-07-16 10:20:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881341295  

Ah so these are hiking paths? So they may be close to each other to the point of bounding boxes being the same. And even if the distance to them is greater than 0 (overlapping point) it can be the same for all of them. Ok, I pushed a fix to the same branch. Could you check it out?

---

## Comment 15

> Username: karme  
> Created at: 2021-07-16 12:28:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881410712  

> Ah so these are hiking paths?   
  
yes, also hiking paths (but also streets / all OSM ways with highway tag)  
  
> So they may be close to each other to the point of bounding boxes being the same. And even if the distance to them is greater than 0 (overlapping point) it can be the same for all of them. Ok, I pushed a fix to the same branch. Could you check it out?  
  
```  
(pick 858855 6502943 1 10000 hiking)  
heap size: 2248  
neighbors size: 100  
qbegin(idx::nearest(pt, 100)) 699usec  
intersections 1  
heap size: 7422  
neighbors size: 2885  
qbegin(idx::nearest(pt, 388521289)) 10669usec  
```  
=> definitely an improvement but still to slow

---

## Comment 16

> Username: awulkiew  
> Created at: 2021-07-16 18:36:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-881641909  

> yes, also hiking paths (but also streets / all OSM ways with highway tag)  
  
Ah ok, by hiking paths I meant GPS tracks uploaded by the users. But you're talking about regular OSM ways/highways.  
  
> definitely an improvement but still to slow  
  
This is interesting. After the fix even the case with k=100 shouldn't gather all of the neighbors but should return right away if the closest neighbor was as far as the next closest node. I tested a case like this, with high number of overlapping boxes (100M boxes in rtree with around 10k overlapping each other):  
```  
  for (size_t i = 0; i < 10000; i++)  
      for (size_t j = 0; j < 10000; j++)  
          t.insert(value_type(B{ {i+0.0, j + 0.0}, {i+100.0, j+100.0} }, linestrings++));  
```  
The result without today's fix:  
```  
linestrings=100000000  
t.size()=100000000  
k=100:   3.14e-05sec  
k=1000000:   0.0064153sec  
k=10000000:  0.0061161sec  
k=100000000: 0.0062848sec  
  
k=100 (note that in this case the iterator ends in different branch)  
heap size: 254  
neighbors size: 100  
k=1000000  
heap size: 2668  
neighbors size: 10200  
k=10000000  
heap size: 2668  
neighbors size: 10200  
k=10000000  
heap size: 2668  
neighbors size: 10200  
```  
The result with today's fix:  
```  
linestrings=100000000  
t.size()=100000000  
k=100:   2.31e-05sec  
k=1000000:   1.16e-05sec  
k=10000000:  1.05e-05sec  
k=100000000: 8.8e-06sec  
  
k=100  
heap size: 255  
neighbors size: 6  
k=1000000  
heap size: 255  
neighbors size: 6  
k=10000000  
heap size: 255  
neighbors size: 6  
k=10000000  
heap size: 255  
neighbors size: 6  
```  
  
So I think I'll wait for your data to reproduce it.  
  
Alternatively you could display some additional debug info like 10 first closest nodes and values in the same branch as before (and maybe in the one below, the one with `continue`), e.g.:  
```  
std::for_each(internal_heap.begin(), internal_heap.begin() + 10, [](auto const& p) { std::cout << p.first << std::endl; });  
std::for_each(neighbors.begin(), neighbors.begin() + 10, [](auto const& p) { std::cout << p.first << std::endl; });  
```

---

## Comment 17

> Username: karme  
> Created at: 2021-07-21 09:58:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-884059339  

I can't reproduce the problem with the simple test - not even using a  
huge dataset. The real-world program still has the problem. The main  
difference between the simple test and the real-world program is that  
the real-world program uses a memory mapped rtree.

---

## Comment 18

> Username: awulkiew  
> Created at: 2021-07-21 13:55:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-884209586  

Interesting. It's possible that data access is simply that much slower. But I'd like to play with it. Maybe I'll be able to reproduce it somehow. Are you using Boost.Interprocess allocator and `managed_shared_memory`?  
  
Btw, now that the fix is merged I'm experimenting in a different branch:  
https://github.com/awulkiew/geometry/tree/feature/rtree_nearest2

---

## Comment 19

> Username: karme  
> Created at: 2021-07-21 14:04:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-884216243  

I cannibalized the real program and now have a relatively small test case:  
```  
karme@planck:~/rtreedebug$ ./run.sh   
+ export BINFMTC_DEBUG=1  
+ BINFMTC_DEBUG=1  
+ export 'BINFMTCXX_GXX_OPTS=-O2 -lpthread'  
+ BINFMTCXX_GXX_OPTS='-O2 -lpthread'  
+ rm -v rtree.bin  
removed 'rtree.bin'  
+ ./rtree_build.cpp rtree.bin  
binfmtc: Execute command-line: g++ -o /tmp/binfmtc754Mw6  -x c++  -Igeometry/include -Iboost_1_76_0 -O2 -lpthread "rtree_build.cpp"  
In file included from geometry/include/boost/geometry.hpp:17:0,  
                 from rtree_build.cpp:10:  
geometry/include/boost/geometry/geometry.hpp:23:2: warning: #warning "using unreleased boost geometry version" [-Wcpp]  
 #warning "using unreleased boost geometry version"  
  ^~~~~~~  
  
real	0m8.211s  
user	0m7.716s  
sys	0m0.480s  
+ ./test.cpp rtree.bin  
binfmtc: Execute command-line: g++ -o /tmp/binfmtcFuXrwO  -x c++  -Igeometry/include -Iboost_1_76_0 -O2 -lpthread "test.cpp"  
In file included from geometry/include/boost/geometry.hpp:17:0,  
                 from test.cpp:4:  
geometry/include/boost/geometry/geometry.hpp:23:2: warning: #warning "using unreleased boost geometry version" [-Wcpp]  
 #warning "using unreleased boost geometry version"  
  ^~~~~~~  
t=0x7f7a4dff4118  
t->size()=3388111  
heap size: 931  
neighbors size: 100  
1074usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
heap size: 2238  
neighbors size: 1345  
2050usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
heap size: 931  
neighbors size: 100  
349usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
heap size: 2238  
neighbors size: 1345  
1959usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
heap size: 931  
neighbors size: 100  
358usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
heap size: 2238  
neighbors size: 1345  
2090usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
  
real	0m4.643s  
user	0m4.356s  
sys	0m0.276s  
```  
  
it is not as slow as the huge dataset but still ~2ms

---

## Comment 20

> Username: karme  
> Created at: 2021-07-21 14:23:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-884231201  

the test is at https://karme.de/delme/rtreedebug.zip  
(looks like it is too big to attach to the issue)

---

## Comment 21

> Username: awulkiew  
> Created at: 2021-07-21 14:34:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-884239953  

Ok, got it. I have something to play with. And I see that you're indeed using Interprocess.

---

## Comment 22

> Username: karme  
> Created at: 2021-07-21 15:16:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-884272234  

> Btw, now that the fix is merged I'm experimenting in a different branch:  
https://github.com/awulkiew/geometry/tree/feature/rtree_nearest2  
  
just tested against this branch (didn't really verify the results) - looks really promising:  
```  
+ ./test.cpp rtree.bin  
binfmtc: Execute command-line: g++ -o /tmp/binfmtckLdRw4  -x c++  -Igeometry/include -Iboost_1_76_0 -O2 -lpthread "test.cpp"  
t=0x7f6d05d49118  
t->size()=3388111  
1139usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
464usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
251usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
365usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
246usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
360usec  
POLYGON((1.00188e+06 6.19042e+06,1.00188e+06 6.19056e+06,1.00195e+06 6.19056e+06,1.00195e+06 6.19042e+06,1.00188e+06 6.19042e+06)) 817846160  
  
real	0m4.642s  
user	0m4.340s  
sys	0m0.288s  
```

---

## Comment 23

> Username: karme  
> Created at: 2021-07-27 12:42:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-887478979  

does it help if I test this branch (https://github.com/awulkiew/geometry/tree/feature/rtree_nearest2) or is it work in progress?

---

## Comment 24

> Username: awulkiew  
> Created at: 2021-07-27 19:42:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-887784187  

@karme Yes, please. I had some problems with running your test case on a machine with windows so I'll wait for you with running it elsewhere.

---

## Comment 25

> Username: awulkiew  
> Created at: 2021-07-29 20:33:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-889438050  

@karme I pushed another change. The internal nodes are now also prioritized based on level so the least possible number of internal nodes should be visited even when the distance to various nodes was equal (e.g. to 0) on the path to the first leaf and therefore the first value.

---

## Comment 26

> Username: karme  
> Created at: 2021-07-30 14:13:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-889921638  

@awulkiew tests look very good!

---

## Comment 27

> Username: awulkiew  
> Created at: 2021-07-30 17:09:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-890033709  

@karme Ok then. I'll create a PR and close this issue after it's merged. Thanks for testing!

---

## Comment 28

> Username: awulkiew  
> Created at: 2021-08-23 00:35:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-903360155  

@karme I had to resolve some conflicts. Could you please check if everything is still fine with performance?

---

## Comment 29

> Username: karme  
> Created at: 2021-08-23 09:47:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-903611890  

> I had to resolve some conflicts. Could you please check if everything is still fine with performance?  
  
performance and results look good

---

## Comment 30

> Username: awulkiew  
> Created at: 2021-08-23 11:05:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/867#issuecomment-903666451  

@karme thanks!
