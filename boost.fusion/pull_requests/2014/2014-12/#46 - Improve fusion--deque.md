# #46 Improve fusion::deque [Merged]

> Username: Flast  
> Created at: 2014-12-04 16:59:35 UTC  
> Updated at: 2014-12-05 02:37:41 UTC  
> Merged at: 2014-12-04 22:17:59 UTC  
> Closed at: 2014-12-04 22:17:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/46  

This PR will fix several deque bugs, including major one: b543d69 .  
It fix failure on `push_{front|back}<deque</* empty */>, T>`, because `{front|back}_extended_deque` and `begin` expect the range `(next_down, next_up)` being valid, not `[next_down, next_up)`.  
  
And 223c7d5 will fix [BP x86_64 C++11 - fusion - deque_make / gcc-4.8.3~c11](http://www.boost.org/development/tests/develop/developer/output/BP%20x86_64%20C++11-boost-bin-v2-libs-fusion-test-deque_make-test-gcc-4-8-3~c11-debug-debug-symbols-off.html).  
  
Tested under  
- GCC 4.8.3 (c++03, c++11, c++14 mode)  
- GCC 4.9.2 (c++03, c++11, c++14 mode)  
- Clang 3.5.0 (c++03, c++11, c++14 mode)  
- MSVC 8.0 - 14.0

---

## Comment 1

> Username: Flast  
> Created_at: 2014-12-05 02:36:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/46#issuecomment-65739486  

Ah, note that some tests are failed until regenerating preprocessed headers.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-12-05 02:37:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/46#issuecomment-65739602  

OK, understood. Well done, Kohei. Feel free to submit a PR for the preprocessed headers if you will.

---
