# #177 More test cases for disjoint [Merged]

> Username: mkaravel  
> Created at: 2014-11-05 06:37:47 UTC  
> Updated at: 2014-11-05 14:13:06 UTC  
> Merged at: 2014-11-05 14:13:06 UTC  
> Closed at: 2014-11-05 14:13:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/177  

- Add a few more test cases, some of them related to the recent bug in the Cartesian segment-segment intersection strategy (see PR #175).  
- Replace calls to `BOOST_CHECK` by calls to `BOOST_CHECK_MESSAGE`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-11-05 10:12:09 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19864167  

~~std::ostringstream~~  
EDIT: no stream is necessary here, see below about BOOST_CHECK_MESSAGE

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-11-05 10:17:13 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19864357  

I don't understand this. There is hardly any difference between SEGMENT + dsv and just wkt...  
  
I see the same implementation occurs in a lot of files. You can just use bg::wkt, which is streamable, and the whole pretty_print_geometry can be removed

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-11-05 10:18:39 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19864418  

You probably mean MULTIPOINT((0, 0),(1,0))...  
~~Does this run? It probably should not.~~  
EDIT: yes, it should run, it is basically OK

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2014-11-05 10:20:22 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19864490  

So this becomes much more readable too, msg << "G1: " << bg::wkt(geometry1) << ", G2" << bg::wkt(geometry2) << " -> " etc.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-11-05 11:24:19 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19867220  

I did this to more easily identify the failing case.  
I will remove all this code and do it another way: pass a case ID string to identify the test case.

---

## Comment 6

> Username: mkaravel  
> Created_at: 2014-11-05 11:24:41 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19867232  

I will use `bg::wkt` and also pass a case ID string.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2014-11-05 11:25:43 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19867271  

OK, passing a case ID is quite convenient indeed, I do that all the time...

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2014-11-05 11:57:58 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19868544  

BOOST_CHECK_MESSAGE is a macro which accepts streaming.  
See e.g. test/algorithms/overlay/self_intersection_points.cpp :  
BOOST_CHECK_MESSAGE(false, "Case " << case_id << " there are self-intersections detected!")

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-11-05 12:06:10 UTC  
> Updated_at: 2014-11-05 14:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#discussion_r19868884  

Yes, I know. I have used it/seen it before.  
I am in the process of making the changes.

---

## Comment 10

> Username: mkaravel  
> Created_at: 2014-11-05 14:06:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#issuecomment-61811128  

@barendgehrels Made changes as promised: the `pretty_print_geometry` class is gone, the calls to `BOOST_CHECK_MESSAGE` have been simplified and each test case has now a unique case ID string related to it.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2014-11-05 14:13:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/177#issuecomment-61812027  

@mkaravel Great! Thanks! This is looking very readable

---
