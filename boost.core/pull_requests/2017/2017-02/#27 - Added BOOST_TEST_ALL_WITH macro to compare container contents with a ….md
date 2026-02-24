# #27 Added BOOST_TEST_ALL_WITH macro to compare container contents with a … [Merged]

> Username: breese  
> Created at: 2017-02-18 12:38:35 UTC  
> Updated at: 2017-02-21 01:20:08 UTC  
> Merged at: 2017-02-21 01:20:08 UTC  
> Closed at: 2017-02-21 01:20:08 UTC  
> Url: https://github.com/boostorg/core/pull/27  

…predicate

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-02-18 16:05:39 UTC  
> Url: https://github.com/boostorg/core/pull/27#issuecomment-280854890  

This looks good. Could you however split the test into two, pass/fail? The current arrangement makes it possible for one of the pass test cases to fail, one of the fail test cases to pass, and the overall count to remain correct.

---
