# #1015 [tests] Add test case in intersection [Merged]

> Username: vissarion  
> Created at: 2022-06-08 08:40:47 UTC  
> Updated at: 2022-07-06 15:30:31 UTC  
> Merged at: 2022-07-06 15:30:30 UTC  
> Closed at: 2022-07-06 15:30:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1015  

Remove rescaling from set operations tests and add https://github.com/boostorg/geometry/issues/630 to test suite.  
  
EDIT: only add issue 630 test case

---

## Review 1 [Changes requested]

> Username: barendgehrels  
> Created_at: 2022-06-08 09:22:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1015#pullrequestreview-999404205  

Sorry about the inconvenience, but please put all the USE_RESCALING and ALTERNATIVE defines back...

📁 test/algorithms/set_operations/difference/Jamfile

```diff
  22 |-     [ run difference_multi.cpp              : : : <define>BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_difference_multi_alternative ]
  21 |+     [ run difference.cpp                    : : : <define>BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE : algorithms_difference_alternative ]
  22 |+     [ run difference_multi.cpp              : : : <define>BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE : algorithms_difference_multi_alternative ]
```

> Username: barendgehrels  
> Created_at: 2022-06-08 09:22:25 UTC  
> Updated_at: 2022-06-08 09:22:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1015#discussion_r892127613  

No, please don't do this!  
We support rescaling for 2 more releases and should unit test it.  
  
This mechanism is designed for this...

> Username: vissarion  
> Created_at: 2022-06-08 09:39:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1015#discussion_r892145971  

OK, I see, I revert back my changes and only add the new test case that is not tested with rescaling since it is failing.

> Username: barendgehrels  
> Created_at: 2022-06-08 10:05:28 UTC  
> Updated_at: 2022-06-08 10:05:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1015#discussion_r892171164  

Thanks for your quick follow-up!

> Username: vissarion  
> Created_at: 2022-06-08 12:46:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1015#discussion_r892314572  

I guess your comment is addressed so I can merge now.


---
