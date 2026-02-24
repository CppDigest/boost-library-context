# #357 Add C++11 std::array point adaptor [Merged]

> Username: norbertwenzel  
> Created at: 2016-08-15 20:39:51 UTC  
> Updated at: 2016-10-07 21:53:31 UTC  
> Merged at: 2016-10-05 21:34:25 UTC  
> Closed at: 2016-10-05 21:34:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/357  

Add point adaptor based on existing Boost.Array adaptor for C++11 std::array. I'm currently not very happy with the preprocessor checks in the test, but since the whole test is really small I think the preprocessor checks are not too bad. Anyway, I'm open to suggestions if changes are required.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-08-17 11:40:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#issuecomment-240387038  

Thanks!  
It looks good to me.

---

## Comment 2

> Username: norbertwenzel  
> Created_at: 2016-09-30 17:03:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#issuecomment-250798138  

Since the merge window is now open again I just wanted to ask if there is anything that needs to be done on my side, before this one is ready.

---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2016-10-01 09:17:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/357#pullrequestreview-2428893  

I'm OK with it (but please update copyright first)

📁 include/boost/geometry/geometries/adapted/std_array.hpp

```diff
   1 |+ // Boost.Geometry (aka GGL, Generic Geometry Library)
   2 |+ 
   3 |+ // Copyright (c) 2010 Alfredo Correa
```

> Username: barendgehrels  
> Created_at: 2016-10-01 09:14:07 UTC  
> Updated_at: 2016-10-02 10:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#discussion_r81446814  

The copyright is not up to date

> Username: norbertwenzel  
> Created_at: 2016-10-01 15:39:47 UTC  
> Updated_at: 2016-10-02 10:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#discussion_r81452162  

This code is basically copied from the `boost::array` adaptor and that's why I did not change it. I don't think my minimal changes are above a threshold of creativity to substantiate any copyright. But I will add my name to this file if you consider it necessary.


📁 test/geometries/boost_array_as_point.cpp

```diff
  40 |+     std::array<double, 3> p5{13,14,15};
  41 |+     std::clog << bg::distance(p4, p5) << std::endl;
  42 |+ #endif //BOOST_NO_CXX11_HDR_ARRAY
```

> Username: barendgehrels  
> Created_at: 2016-10-01 09:16:34 UTC  
> Updated_at: 2016-10-02 10:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#discussion_r81446852  

Nice. This is not about your change, but we should update this test. Writing to clog is not really a unit test, apparently this was never really finished. Anyway, that is not an objective for merging.

> Username: norbertwenzel  
> Created_at: 2016-10-01 15:51:01 UTC  
> Updated_at: 2016-10-02 10:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#discussion_r81452359  

At first I thought this test is incomplete too, but now I think it's not too bad. Since this test merely tests if the new point type is recognized by using the relatively simple `bg::distance()` algorithm and hope this test compiles. So the only thing I'd maybe add to these adaptor tests would be a check for the distance results in order to test if the point layout has been recognized correctly. But that's probably a question of taste.


---

## Comment 4

> Username: barendgehrels  
> Created_at: 2016-10-01 09:18:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#issuecomment-250902441  

And thanks for your contribution and notification. If it is merged it will be merged to master too.  
We also have to update the release notes for this.

---

## Comment 5

> Username: norbertwenzel  
> Created_at: 2016-10-02 10:28:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#issuecomment-250964515  

@barendgehrels I've amended the copyright info in `std_array.hpp`.

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2016-10-05 21:34:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/357#issuecomment-251806352  

@norbertwenzel  thanks! I will merge it.

---
