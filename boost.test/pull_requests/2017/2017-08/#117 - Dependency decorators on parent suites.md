# #117 Dependency decorators on parent suites [Closed]

> Username: namezero111111  
> Created at: 2017-08-06 11:10:02 UTC  
> Updated at: 2017-12-30 08:27:42 UTC  
> Closed at: 2017-12-30 08:27:42 UTC  
> Url: https://github.com/boostorg/test/pull/117  

Dependency decorators on parent suites are not honored when a nested test selected to run via command line.  
See ticket #13149   
  
https://svn.boost.org/trac10/ticket/13149

---

## Comment 1

> Username: namezero111111  
> Created_at: 2017-08-06 15:54:57 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-320515268  

Hello,  
  
Yes, please check this minimal example:  
  
http://coliru.stacked-crooked.com/a/018bd236999a68f3

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2017-09-06 08:05:08 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-327407785  

Thanks!  
  
Merged to branch `topic/13181-collection-compare-missing-typename` but tests are failing, I will check.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2017-09-06 10:28:25 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-327442974  

Sorry, wrong ticket!

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2017-09-06 10:31:41 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-327443682  

Merged to branch `topic/13149-dependency-decorators-on-parent-suites` , waiting for the tests to cycle

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2017-12-27 17:17:07 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-354145487  

Tests failing: `run-by-name-or-label-test` broken after applying the patch.

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2017-12-29 19:57:41 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-354493521  

The branch origin/topic/13149-dependency-decorators-on-parent-suites is ready to merge. It would be nice if you can review the changes to your initial patch.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2017-12-30 08:27:42 UTC  
> Url: https://github.com/boostorg/test/pull/117#issuecomment-354534168  

On develop, thanks

---
