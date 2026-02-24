# #887 [traverse] reverse meaning of isolation in difference [Merged]

> Username: barendgehrels  
> Created at: 2021-07-08 10:54:18 UTC  
> Updated at: 2021-07-28 11:10:33 UTC  
> Merged at: 2021-07-28 11:10:28 UTC  
> Closed at: 2021-07-28 11:10:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/887  

This fixes #869 , the recently found problem in difference and interconnected interiors, and should fix similar cases too (though they were not yet in the test suite).  
  
The root cause was that isolated interior rings were detected but in case of difference, it's meaning should be reversed.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2021-07-08 10:55:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876339989  

Note that I get an unrelated error  
```  
clang-linux.compile.c++.without-pch detail/bin/algorithms_visit.test/clang-linux-6.0.0/debug/visit.o  
In file included from detail/visit.cpp:18:  
../boost/geometry/geometries/adapted/std_any.hpp:42:16: error: no template named 'any_cast' in namespace 'std'; did you mean simply 'any_cast'?  
        return std::any_cast<T>(any_ptr);  
               ^~~~~~~~~~~~~  
               any_cast  
../boost/any.hpp:249:17: note: 'any_cast' declared here  
    ValueType * any_cast(any * operand) BOOST_NOEXCEPT  
```  
but maybe it's fixed in the meantime

---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-07-08 10:55:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/887#pullrequestreview-701921096  

📁 include/boost/geometry/algorithms/detail/overlay/traversal_switch_detector.hpp

```diff
 417 |+                             = reverseMeaningInOp
 418 |+                             ? prop.isolated == isolation_no
 419 |+                             : prop.isolated == isolation_yes;
```

> Username: barendgehrels  
> Created_at: 2021-07-08 10:55:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#discussion_r666087607  

this is the fix

> Username: barendgehrels  
> Created_at: 2021-07-14 12:04:22 UTC  
> Updated_at: 2021-07-14 12:04:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#discussion_r669549249  

The first version did have `prop.isolated == isolation_no` but that is the default, we should not revert all of them into yes.  
  
But it now does not look symmetrical, theoretically there should be cases where reversal should be necessary - but we don't have them currently in our test set.

> Username: kleunen  
> Created_at: 2021-07-14 12:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#discussion_r669565829  

Can you reverse one of the test cases? To make sure the other option also gets triggered by test suite?

> Username: barendgehrels  
> Created_at: 2021-07-14 12:46:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#discussion_r669582060  

We test all our difference operations in two ways, so both `a - b` and `b - a` . If that is what you mean.   
  
Or you probably mean (what I call) inverse them, so make a large polygon around a polygon and make the polygon itself an interior of that. Yes, I could do that, I will try to construct a case where this should happen. I now know where I’m looking for. I will do that, but not today.  
  
Thanks.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2021-07-08 10:57:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876341507  

Also I get, unrelated:  
```  
In file included from ../boost/geometry/strategies/convex_hull/cartesian.hpp:16:  
../boost/geometry/strategy/cartesian/side_robust.hpp:53:20: error: non-constant-expression cannot be narrowed from type 'typename coordinate_type<point_xy<int, cartesian> >::type' (aka 'int') to 'double' in initializer list [-Wc++11-narrowing]  
        vec2d pa { get<0>(p1), get<1>(p1) };  
```

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-07-08 12:08:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876385008  

@barendgehrels I don't know what's heppening with the first one because the code is behind `#ifndef BOOST_NO_CXX17_HDR_ANY`. Maybe there is something wrong with clang-6 and we'd need a workaround. Are you setting `-std`?  
  
Regarding the second one. This was already fixed: https://github.com/boostorg/geometry/pull/878

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2021-07-08 14:44:05 UTC  
> Updated_at: 2021-07-08 14:47:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876499922  

> @barendgehrels I don't know what's heppening with the first one because the code is behind `#ifndef BOOST_NO_CXX17_HDR_ANY`. Maybe there is something wrong with clang-6 and we'd need a workaround. Are you setting `-std`?  
>   
> Regarding the second one. This was already fixed: #878  
  
I'm just calling `b2 --toolset=clang` and my clang version is `clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)`  
(I've also `clang version 5.0.2 (tags/RELEASE_502/final)` but that gives me other errors, and more)

---

## Comment 6

> Username: vissarion  
> Created_at: 2021-07-08 15:53:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876553554  

> > @barendgehrels I don't know what's heppening with the first one because the code is behind `#ifndef BOOST_NO_CXX17_HDR_ANY`. Maybe there is something wrong with clang-6 and we'd need a workaround. Are you setting `-std`?  
> > Regarding the second one. This was already fixed: #878  
>   
> I'm just calling `b2 --toolset=clang` and my clang version is `clang version 6.0.0-1ubuntu2 (tags/RELEASE_600/final)`  
> (I've also `clang version 5.0.2 (tags/RELEASE_502/final)` but that gives me other errors, and more)  
  
I am also getting something similar with `gcc`  
  
```  
gcc.compile.c++ ../../bin.v2/libs/geometry/test/algorithms/detail/algorithms_visit.test/gcc-5~c++14/debug/threading-multi/visibility-hidden/visit.o  
In file included from test/algorithms/detail/visit.cpp:18:0:  
../../boost/geometry/geometries/adapted/std_any.hpp:19:15: fatal error: any: No such file or directory  
compilation terminated.  
```

---

## Comment 7

> Username: awulkiew  
> Created_at: 2021-07-08 17:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876604272  

@barendgehrels @vissarion AFAIU C++17 headers support was added to Boost.Config in 1.76.  
Which version of Boost.Config do you have?  
When did you last update the whole Boost (all of the modules)?

---

## Comment 8

> Username: kleunen  
> Created_at: 2021-07-08 18:02:58 UTC  
> Updated_at: 2021-07-09 06:26:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876637538  

I have the following test case which was correct under the old version, but seems to be incorrect, after this patch:  
https://wandbox.org/permlink/2zBpf9zFb1Uepu7n

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2021-07-08 21:08:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876747137  

>   
>   
> @barendgehrels @vissarion AFAIU C++17 headers support was added to Boost.Config in 1.76.  
> Which version of Boost.Config do you have?  
> When did you last update the whole Boost (all of the modules)?  
  
@awulkiew ah, of course, sorry. Will update soon, it's long ago indeed.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2021-07-08 21:09:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876747367  

>   
>   
> I have the following test case which was correct under the under the old version, but seems to be incorrect, after this patch:  
> https://wandbox.org/permlink/2zBpf9zFb1Uepu7n  
  
Thanks! I will look at it soon

---

## Comment 11

> Username: awulkiew  
> Created_at: 2021-07-08 23:57:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876815951  

LGTM. Is this a bugfix? Would you like to release it with 1.77 if possible? Could we merge https://github.com/boostorg/geometry/pull/886 before this PR in case you wanted to postpone this one to 1.78?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2021-07-09 06:17:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-876944127  

> LGTM. Is this a bugfix? Would you like to release it with 1.77 if possible? Could we merge #886 before this PR in case you wanted to postpone this one to 1.78?  
  
Wouter found a problem in another case, I’ve still to check that. Therefore it’s better to postpone it to 1.78

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2021-07-14 09:04:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-879725207  

> I have the following test case which was correct under the under the old version, but seems to be incorrect, after this patch:  
> https://wandbox.org/permlink/2zBpf9zFb1Uepu7n  
  
I can reproduce it and I've a reduced version for it. I will create a separate issue for this, to add this testcase, and will try to fix it (large input... it might take a while).  
It's a combination of complex input multipolygon - while the solved case is simple. So alternatively we can merge this already and (try to) fix it afterwards

---

## Comment 14

> Username: kleunen  
> Created_at: 2021-07-14 09:28:11 UTC  
> Updated_at: 2021-07-14 09:29:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-879741943  

> Wouter found a problem in another case, I’ve still to check that. Therefore it’s better to postpone it to 1.78  
  
yes, there is no need to rush to a fix. In many cases actually the sym_difference works well. I sort the input polygons now by size and if I do a sym_difference in this order the output is fine. So I really think it is a corner case and you don't want to break functionality working well for other users.

---

## Comment 15

> Username: barendgehrels  
> Created_at: 2021-07-14 12:02:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#issuecomment-879832352  

The regression in the two cases referred by #888 should be fixed now

---

## Review 16 [Commented]

> Username: barendgehrels  
> Created_at: 2021-07-14 12:05:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/887#pullrequestreview-706192019  

📁 include/boost/geometry/algorithms/detail/overlay/traversal_switch_detector.hpp

```diff
 593 | 
 594 |+ #if defined(BOOST_GEOMETRY_DEBUG_TRAVERSAL_SWITCH_DETECTOR)
 595 |+     void debug_show_results()
```

> Username: barendgehrels  
> Created_at: 2021-07-14 12:05:56 UTC  
> Updated_at: 2021-07-14 12:06:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/887#discussion_r669550410  

this shows quite a neat list of results and I would like to keep it.  
  
For example  
```  
BEGIN SWITCH DETECTOR (region_ids and isolation) REVERSE_2  
 ADD (s:0, m:0) TO REGION 1  
 ADD (s:1, m:0) TO REGION 2  
 ADD (s:1, m:1) TO REGION 3  
END SWITCH DETECTOR  
REGION 1 multiple [turns 0 1 2] regions { 2 : via [ 0 1 2 ] }  
REGION 2 yes [turns 0 1 2 3] regions { 1 : via [ 0 1 2 ] } { 3 : via [ 3 ] }  
REGION 3 yes [turns 3] regions { 2 : via [ 3 ] }  
II 0 (50, 70) ->  [0/1] (1 false) / (2 false)  
II 1 (70, 70) ->  [0/1] (1 false) / (2 false)  
II 2 (70, 50) ->  [0/1] (1 false) / (2 false)  
II 3 (50, 50) ->  [1/1] (2 true) / (3 true)  
```


---
