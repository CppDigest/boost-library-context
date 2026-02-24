# #638 - rfc7230 tidy and tests [Closed]

> Username: vinniefalco  
> Created at: 2017-07-11 21:22:43 UTC  
> Updated at: 2018-05-01 18:32:41 UTC  
> Closed at: 2018-05-01 18:32:41 UTC  
> Url: https://github.com/boostorg/beast/issues/638  

is_tchar could use a test, maybe some benchmarks. there should be an "obviously correct" function in the test which compares against the table driven output. Think about making is_tchar and those routines public interfaces to assist users.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-13 04:28:44 UTC  
> Url: https://github.com/boostorg/beast/issues/638#issuecomment-314967508  

All the various `is_token_char` and other routines should be consolidated into detail/rfc7230.hpp with an eye towards making them public. Rename to be consistent with the rfc.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-04-24 17:52:31 UTC  
> Url: https://github.com/boostorg/beast/issues/638#issuecomment-384022598  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-05-01 18:32:32 UTC  
> Url: https://github.com/boostorg/beast/issues/638#issuecomment-385750206  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
