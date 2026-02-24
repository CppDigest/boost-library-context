# #231 Fixing missing inline, adding link test [Merged]

> Username: henryiii  
> Created at: 2019-10-07 17:54:25 UTC  
> Updated at: 2019-10-08 08:10:24 UTC  
> Merged at: 2019-10-08 08:10:24 UTC  
> Closed at: 2019-10-08 08:10:24 UTC  
> Url: https://github.com/boostorg/histogram/pull/231  

There is an inline missing in one of the recent updates, which makes Boost.Histogram unusable when linking two files. I've added a test that fails if this happens again, and fixed the issue.

---

## Review 1 [Commented]

> Username: henryiii  
> Created_at: 2019-10-07 17:56:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/231#pullrequestreview-298305075  

📁 test/Jamfile

```diff
  72 |+ # Verify that the compile succeeds when linking (missing inline detection) 
  73 |+ alias compiles :
  74 |+     [ link link_2_test.cpp link_1_test.cpp ]
```

> Username: henryiii  
> Created_at: 2019-10-07 17:54:52 UTC  
> Updated_at: 2019-10-07 18:02:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/231#discussion_r332155114  

Please check my Jamfile, I'm not a regular Jammer. ;) (Edit: looks like Jam is used to build the tests, at least on Windows, so CI should at least help check it.)


📁 test/link_1_test.cpp

```diff
   1 |+ #include <boost/histogram.hpp>
   2 |+ #include <boost/histogram/axis/ostream.hpp>
   3 |+ #include <boost/histogram/ostream.hpp>
```

> Username: henryiii  
> Created_at: 2019-10-07 17:55:22 UTC  
> Updated_at: 2019-10-07 18:00:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/231#discussion_r332155357  

Any other files not included by `<boost/histogram.hpp>` should be added here.


📁 test/link_2_test.cpp

```diff
  19 |+ 
  20 |+   int a = do_nothing();
  21 |+   BOOST_TEST_EQ(a, 7);
```

> Username: henryiii  
> Created_at: 2019-10-07 17:55:59 UTC  
> Updated_at: 2019-10-07 18:00:00 UTC  
> Url: https://github.com/boostorg/histogram/pull/231#discussion_r332155708  

I actually only build and link, so this really doesn't even need a BOOST_TEST.


---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-10-08 08:10:13 UTC  
> Url: https://github.com/boostorg/histogram/pull/231#issuecomment-539401367  

Thank you, having such a test is a very good idea. There are dedicated link tests in Boost, I will look into this, but for now the test seems to do the job.

---
