# #384 Update CI to use containers for old compilers [Merged]

> Username: mborland  
> Created at: 2023-05-31 12:02:43 UTC  
> Updated at: 2023-06-06 11:27:22 UTC  
> Merged at: 2023-06-06 10:41:10 UTC  
> Closed at: 2023-06-06 10:41:10 UTC  
> Url: https://github.com/boostorg/test/pull/384  



---

## Review 1 [Commented]

> Username: raffienficiaud  
> Created_at: 2023-06-06 11:19:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/384#pullrequestreview-1464939265  

📁 test/Jamfile.v2

```diff
 143 | :
 144 |-   [ boost.test-self-test run : framework-ts : result-report-test : : baseline-outputs/result-report-test.pattern baseline-outputs/result_report_test.pattern.default_behaviour ]
 144 |+   # [ boost.test-self-test run : framework-ts : result-report-test : : baseline-outputs/result-report-test.pattern baseline-outputs/result_report_test.pattern.default_behaviour ]
```

> Username: raffienficiaud  
> Created_at: 2023-06-06 11:19:18 UTC  
> Updated_at: 2023-06-06 11:19:19 UTC  
> Url: https://github.com/boostorg/test/pull/384#discussion_r1219448121  

Any insights on why we are loosing so many tests?

> Username: mborland  
> Created_at: 2023-06-06 11:26:17 UTC  
> Updated_at: 2023-06-06 11:26:18 UTC  
> Url: https://github.com/boostorg/test/pull/384#discussion_r1219458413  

The two main ones I saw are:  
  
1) UBSAN picking up things that are in other boost libraries (Boost.IO showed up a few times)  
  
2) A number of the tests that are marked as run-fail are failing, but for some reason they aren't being marked with failed-as-expected  
  
This is also littered with deprecation warnings as a number of Boost libraries are dropping C++03 support in 1.84. Now that baseline CI has been re-established I was going to go through the commits labeled as ignoring tests and hopefully fix the underlying issues.

> Username: raffienficiaud  
> Created_at: 2023-06-06 11:27:21 UTC  
> Url: https://github.com/boostorg/test/pull/384#discussion_r1219461039  

Thanks for your work!


---
