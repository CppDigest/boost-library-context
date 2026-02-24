# #212 Comment 'ar' parameter of assertion_result to avoid warning [Closed]

> Username: mloskot  
> Created at: 2019-03-20 19:02:11 UTC  
> Updated at: 2019-04-05 16:22:02 UTC  
> Closed at: 2019-03-21 06:14:15 UTC  
> Url: https://github.com/boostorg/test/pull/212  

This should help to avoid the warning compiling tests that (indirectly) use this public header with `-Wunused-parameter` or equivalent flag. For example, ~100 instances of the warning is issued when compiling tests of Boost.GIL.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2019-03-20 19:24:45 UTC  
> Url: https://github.com/boostorg/test/pull/212#issuecomment-474992832  

Testing in branch `topic/PR-212-ar-parameter-assertion_result-warning`.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2019-03-21 06:13:16 UTC  
> Url: https://github.com/boostorg/test/pull/212#issuecomment-475122494  

In develop, thanks

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-03-21 06:42:14 UTC  
> Url: https://github.com/boostorg/test/pull/212#issuecomment-475126599  

Thanks for prompt merge

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2019-03-22 00:02:44 UTC  
> Url: https://github.com/boostorg/test/pull/212#issuecomment-475447758  

In master.

---
