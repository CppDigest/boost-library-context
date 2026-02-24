# #741 Fix/handle as touch [Merged]

> Username: barendgehrels  
> Created at: 2020-08-05 09:29:03 UTC  
> Updated at: 2020-08-12 08:37:10 UTC  
> Merged at: 2020-08-10 18:46:01 UTC  
> Closed at: 2020-08-10 18:46:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/741  

Mainly changes in `get_turn_info`. It is about specific situations. I provided ASCII-figures in the comments.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-08-05 09:34:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/741#pullrequestreview-461498066  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 555 |+     //! 1 if it is located left
 556 |+     //! -1 if it is located right
 557 |+     //! 0 if it is collinear
```

> Username: barendgehrels  
> Created_at: 2020-08-05 09:34:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#discussion_r465598973  

This is kind of a generic function, I think it works very nice and indicates better the intentions (actually it could be used in several places in this complex file). I use it now in the new function. I could move it and unit-test it separately.  
  
The only "disadvantage" is that sides are not cached, that means that (especially if we use it more frequently), it might calculate the same side several times. However, solving that would require additional complexity (i.e. pass 3 sides, and it is called four times, so at that place we need to add 12 (a bit less, some are duplicate) side variables.  
  
Please let me know your opinion.

> Username: vissarion  
> Created_at: 2020-08-05 09:59:09 UTC  
> Updated_at: 2020-08-05 09:59:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#discussion_r465613015  

It depends whether this is indeed a bottleneck in real use cases. That is, if it is common for an application to calculate several sides and the performance gain is significant then caching sides could worth it.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-08-05 09:36:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/741#pullrequestreview-461499558  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
1384 |+         bool handle_as_equal = method == 'e';
1385 |+         bool const handle_as_collinear = method == 'c';
1386 |+         bool const handle_as_degenerate = method == '0';
```

> Username: barendgehrels  
> Created_at: 2020-08-05 09:36:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#discussion_r465600193  

This block is changed from `switch` to boolean variables and `if`, mainly because in some cases first the `touch_interior` is tried but then it falls back to a `touch`, and `switch` is not convenient for that.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2020-08-05 09:37:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/741#pullrequestreview-461500304  

📁 test/algorithms/set_operations/union/union.cpp

```diff
 525 |                 1, 0, -1, 21.07612);
 526 |     test_one<Polygon, Polygon, Polygon>("buffer_rt_r_rev", buffer_rt_r[1], buffer_rt_r[0],
 527 |                 1, 0, -1, 21.07612);
```

> Username: barendgehrels  
> Created_at: 2020-08-05 09:37:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#discussion_r465600766  

Fixing these was actually the main purpose of this PR


---

## Comment 4

> Username: barendgehrels  
> Created_at: 2020-08-10 18:45:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#issuecomment-671524086  

Thanks for your reviews.  
@vissarion : for now it's not yet a bottleneck but I'll keep an eye on it if we use it more.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2020-08-10 20:46:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#issuecomment-671581454  

This change introduced a regression, failures with msvc-14.1 (VS2017) 32bit:  
  
test/algorithms/overlay/get_turn_info.cpp  
```  
test/algorithms/overlay/get_turn_info.cpp(123): error: in "test_main_caller( argc_ argv )": blr1_r method: tt detected: ii expected: iu  
test/algorithms/overlay/get_turn_info.cpp(123): error: in "test_main_caller( argc_ argv )": brl1   method: tt detected: ii expected: iu  
test/algorithms/overlay/get_turn_info.cpp(123): error: in "test_main_caller( argc_ argv )": t-clr1_r method: tt detected: ii expected: iu  
test/algorithms/overlay/get_turn_info.cpp(123): error: in "test_main_caller( argc_ argv )": t-crl1   method: tt detected: ii expected: iu  
```  
  
test/algorithms/touches/touches.hpp  
```  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 8,-8 5,-8 8,0 8)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(54): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 8,-8 5,-8 8,0 8)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 8,-8 5,-8 8,0 8)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 8,-8 5,-8 8,0 8)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 8,-8 5,-8 8,0 8)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 6,-6 3,-6 6,0 6)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(54): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 6,-6 3,-6 6,0 6)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 6,-6 3,-6 6,0 6)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 6,-6 3,-6 6,0 6)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: 1 detected: 0  
d:/lib/modular-boost/libs/geometry/test/algorithms/touches/test_touches.hpp(79): error: in "test_main_caller( argc_ argv )": touches: POLYGON((0 6,-6 3,-6 6,0 6)) with POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: 1 detected: 0  
```  
  
test/algorithms/relate/relate_areal_areal.cpp  
```  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(128): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 0,0 10,10 10,10 5,4 7,4 3,10 5,10 0,0 0)) and POLYGON((0 0,0 10,10 10,10 0,0 0),(10 5,5 6,5 4,10 5)) -> Expected: 2FF11F212 detected: 21211F2F2  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(169): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 0,0 10,10 10,10 5,4 7,4 3,10 5,10 0,0 0)) and POLYGON((0 0,0 10,10 10,10 0,0 0),(10 5,5 6,5 4,10 5)) -> Expected: 2FF11F212  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(158): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 0,0 10,10 10,10 5,3 8,3 2,10 5,10 0,0 0)) and POLYGON((0 0,0 10,10 10,10 0,0 0),(10 5,4 7,4 3,10 5)) -> Expected: 2FF11F212 detected: 21211F2F2  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(158): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 8,-8 5,-8 8,0 8)) and POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: FF2F01212 detected: 212F01212  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(158): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 6,-6 3,-6 6,0 6)) and POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: FF2F01212 detected: 212F01212  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(128): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 0,0 10,10 10,10 5,4 7,4 3,10 5,10 0,0 0)) and POLYGON((0 0,0 10,10 10,10 0,0 0),(10 5,5 6,5 4,10 5)) -> Expected: 2FF11F212 detected: 21211F2F2  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(169): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 0,0 10,10 10,10 5,4 7,4 3,10 5,10 0,0 0)) and POLYGON((0 0,0 10,10 10,10 0,0 0),(10 5,5 6,5 4,10 5)) -> Expected: 2FF11F212  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(158): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 0,0 10,10 10,10 5,3 8,3 2,10 5,10 0,0 0)) and POLYGON((0 0,0 10,10 10,10 0,0 0),(10 5,4 7,4 3,10 5)) -> Expected: 2FF11F212 detected: 21211F2F2  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(158): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 8,-8 5,-8 8,0 8)) and POLYGON((0 0,0 10,10 10,10 0,0 0), (0 8, 8 5, 8 8, 0 8)) -> Expected: FF2F01212 detected: 212F01212  
d:/lib/modular-boost/libs/geometry/test/algorithms/relate/test_relate.hpp(158): error: in "test_main_caller( argc_ argv )": relate: POLYGON((0 6,-6 3,-6 6,0 6)) and POLYGON((0 0,0 10,10 10,10 0,0 0), (0 6, 6 3, 6 6, 0 6)) -> Expected: FF2F01212 detected: 212F01212  
```

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2020-08-10 20:53:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#issuecomment-671584001  

Oops, thanks, I was not aware of this. Can't fix it right now, it will be Wednesday

---

## Comment 7

> Username: awulkiew  
> Created_at: 2020-08-10 21:01:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#issuecomment-671587541  

Ok, thanks.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-08-11 21:12:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#issuecomment-672282113  

Btw, it also fails with gcc-6.3.0:  
https://app.circleci.com/pipelines/github/boostorg/geometry?branch=develop  
so you don't need to use VS to reproduce it.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2020-08-12 08:37:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/741#issuecomment-672735505  

>   
>   
> Btw, it also fails with gcc-6.3.0:  
> https://app.circleci.com/pipelines/github/boostorg/geometry?branch=develop  
> so you don't need to use VS to reproduce it.  
  
Yeah, thanks. I can reproduce it indeed.

---
