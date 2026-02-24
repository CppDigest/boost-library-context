# #51 report_errors with expected failures [Merged]

> Username: HDembinski  
> Created at: 2019-04-18 14:11:48 UTC  
> Updated at: 2019-06-27 09:11:51 UTC  
> Merged at: 2019-04-20 06:55:50 UTC  
> Closed at: 2019-04-20 06:55:50 UTC  
> Url: https://github.com/boostorg/core/pull/51  

Allow test to pass when it has exactly N expected failures. This is useful for `lightweight_test_test5.cpp` to actually test for the correct number of errors, and maybe useful elsewhere.

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-04-18 14:37:42 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-484537621  

The `return` should be outside the `if`, and the documentation needs to be updated.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2019-04-19 10:45:38 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-484848591  

One of appveyor builds didn't run. Could you try to restart it?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-04-19 13:53:07 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-484902726  

On reflection, I'm not entirely sure whether it won't be better to just return the number of errors from `report_errors`, as the test implies.

---

## Comment 4

> Username: HDembinski  
> Created_at: 2019-04-19 15:24:10 UTC  
> Updated_at: 2019-04-19 15:25:59 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-484930034  

I think it is worse. This was the old code:  
```  
return report_errors() == 10  
```  
It only "worked" because `report_errors()` returned 1, in contradiction to documentation. But even if it did return 10, then this would just have the opposite of the desired effect. If `report_errors()` returns the number of errors, then one must write `report_errors() != 10`. That is quite awkward.  
  
The mistake was already made once, which shows that this is not a good design.

---

## Comment 5

> Username: pdimov  
> Created_at: 2019-04-19 16:56:15 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-484956090  

Yes, with == 10 it needs to be a run-fail test. Which incidentally we want, because we can see the output of the failed tests.

---

## Comment 6

> Username: glenfe  
> Created_at: 2019-04-19 18:15:10 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-484975845  

`report_errors()` returning the number of errors seems better to me.

---

## Comment 7

> Username: Lastique  
> Created_at: 2019-06-26 16:12:33 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-505943273  

Given that in most tests we have `return boost::report_errors();` in the end of `main()`, I think this change is not correct. The returned value will get truncated to 8 bits, and in the shell the calling process may receive 0 result code while there are a multiple of 256 failed tests.  
  
I think, there should be a separate API that allows to obtain the number of failures.

---

## Comment 8

> Username: Lastique  
> Created_at: 2019-06-26 16:13:46 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-505943687  

I propose to revert this change for 1.71.

---

## Comment 9

> Username: HDembinski  
> Created_at: 2019-06-26 16:21:18 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-505946524  

> The returned value will get truncated to 8 bits, and in the shell the calling process may receive 0 result code while there are a multiple of 256 failed tests.  
  
Could you please point me to the documentation which shows that the return type `int` is truncated to `char`?  
  
My original proposal was to pass the expected number of errors as an argument to `boost::report_errors`, which would side-step this issue.

---

## Comment 10

> Username: pdimov  
> Created_at: 2019-06-26 16:23:56 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-505947458  

> and in the shell the calling process may receive 0 result code while there are a multiple of 256 failed tests.  
  
I'm willing to take that risk.

---

## Comment 11

> Username: Lastique  
> Created_at: 2019-06-26 16:26:53 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-505948582  

> Could you please point me to the documentation which shows that the return type `int` is truncated to `char`?  
  
Not `char`, rather `uint8_t`. For example, it follows from [`WEXITSTATUS`](https://linux.die.net/man/2/waitpid).  
  
You can test yourself:  
  
```  
int main()  
{  
    return -1;  
}  
```  
  
```  
$ ./return_code ; echo $?  
255  
```  
  
> I'm willing to take that risk.  
  
Umm, I'd much rather not. We have tons of tests in Boost like that, and who knows how many outside.

---

## Comment 12

> Username: HDembinski  
> Created_at: 2019-06-27 05:19:41 UTC  
> Updated_at: 2019-06-27 05:20:47 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-506193918  

@Lastique (why don't you use your real name?): You want to take a feature away instead of amending it, that is like demolishing a house because you don't like the color of the walls. You only revert things when they are wrong beyond repair or when it is really urgent to avoid harm. Neither is the case here. This feature solves an issue, namely that a test with several expected failures would pass even if at least one of the expected failures happened. It did not detect how many of the expected failures actually happened. In contrast to the highly contrived situation that you are describing, this is one that I actually encountered while developing Boost.Core.

---

## Comment 13

> Username: Lastique  
> Created_at: 2019-06-27 09:11:51 UTC  
> Url: https://github.com/boostorg/core/pull/51#issuecomment-506263733  

> You want to take a feature away instead of amending it, that is like demolishing a house because you don't like the color of the walls.  
  
No, I want to revert the commit that introduced a bug. I still want to implement what you want but do it the right way. The revert part is important and IMO should happen faster since we're on the brink of 1.71 and I don't want a broken version of `lightweight_test.hpp` to be released. Better release the old version of the header without the feature than the new one that breaks users' tests.

---
