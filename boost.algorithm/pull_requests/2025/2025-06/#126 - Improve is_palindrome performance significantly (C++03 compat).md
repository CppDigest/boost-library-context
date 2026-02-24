# #126 Improve is_palindrome performance significantly (C++03 compat) [Open]

> Username: jeking3  
> Created at: 2025-06-24 01:52:49 UTC  
> Updated at: 2025-06-24 20:38:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/126  

Refactored `is_palindrome` to use std::equal yielding a 58% performance gain in limited testing (x86_64, gcc 12.2).  This has fewer branches and leverages the optimization already done on the std::equal implementation.  
  
Before: 6.953285 secs  
```  
jking@pulsar:~/boost/libs/algorithm/performance$ ../../../bin.v2/libs/algorithm/performance/perf_is_palindrome.test/gcc-12/release/x86_64/link-static/perf_is_palindrome  
Total runs: 12000000  
Total palindromes found: 6000000  
Total elapsed time: 6.95016 seconds  
Average time per is_palindrome: 0.57918 microseconds  
jking@pulsar:~/boost/libs/algorithm/performance$ ../../../bin.v2/libs/algorithm/performance/perf_is_palindrome.test/gcc-12/release/x86_64/link-static/perf_is_palindrome  
Total runs: 12000000  
Total palindromes found: 6000000  
Total elapsed time: 6.95641 seconds  
Average time per is_palindrome: 0.579701 microseconds  
```  
  
After: 2.915925 secs  
```  
jking@pulsar:~/boost/libs/algorithm/performance$ ../../../bin.v2/libs/algorithm/performance/perf_is_palindrome.test/gcc-12/release/x86_64/link-static/perf_is_palindrome  
Total runs: 12000000  
Total palindromes found: 6000000  
Total elapsed time: 2.92422 seconds  
Average time per is_palindrome: 0.243685 microseconds  
jking@pulsar:~/boost/libs/algorithm/performance$ ../../../bin.v2/libs/algorithm/performance/perf_is_palindrome.test/gcc-12/release/x86_64/link-static/perf_is_palindrome  
Total runs: 12000000  
Total palindromes found: 6000000  
Total elapsed time: 2.90763 seconds  
Average time per is_palindrome: 0.242302 microseconds  
```  
  
Yielding a 58.06% performance gain.

---

## Review 1 [Commented]

> Username: jgopel  
> Created_at: 2025-06-24 19:08:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/algorithm/pull/126#pullrequestreview-2955002986  

📁 performance/perf_is_palindrome.cpp

```diff
  23 |+     test_cases.push_back("abcdefg");
  24 |+     test_cases.push_back("abccba");
  25 |+     test_cases.push_back("abccbx");
```

> Username: jgopel  
> Created_at: 2025-06-24 19:08:11 UTC  
> Updated_at: 2025-06-24 19:08:12 UTC  
> Url: https://github.com/boostorg/algorithm/pull/126#discussion_r2164712523  

**question:** Why not have these `push_back()` cases in the small set?

> Username: jeking3  
> Created_at: 2025-06-24 20:38:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/126#discussion_r2164859073  

Good callout; they were leftover from a previous test iteration... but no need to remove them at this point.


---
