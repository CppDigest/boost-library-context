# #16 Fix TRAC 11226 by partially reverting b959ed0c33ef28ec978689a8b1235661fc... [Merged]

> Username: jefftrull  
> Created at: 2015-04-23 04:08:53 UTC  
> Updated at: 2015-04-23 04:53:15 UTC  
> Merged at: 2015-04-23 04:51:52 UTC  
> Closed at: 2015-04-23 04:51:52 UTC  
> Url: https://github.com/boostorg/phoenix/pull/16  

Restores the direct return of the let expression's value in place of the assignment-then-return code, to fix compile errors when the let expression's value is void.  Also, adds a test to ensure such expressions will compile properly in the future.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-04-23 04:51:21 UTC  
> Url: https://github.com/boostorg/phoenix/pull/16#issuecomment-95431515  

Note: There are some failing tests, but those happened after the merge from master.

---

## Comment 2

> Username: djowel  
> Created_at: 2015-04-23 04:53:08 UTC  
> Updated_at: 2015-04-23 04:53:15 UTC  
> Url: https://github.com/boostorg/phoenix/pull/16#issuecomment-95431928  

merged. thanks, @jefftrull

---
