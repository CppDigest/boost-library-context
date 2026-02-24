# #77 - integer 32/64 bit files have the wrong range [Closed]

> Username: vinniefalco  
> Created at: 2020-04-23 00:15:58 UTC  
> Updated at: 2020-09-01 23:03:51 UTC  
> Closed at: 2020-09-01 23:03:51 UTC  
> Url: https://github.com/boostorg/json/issues/77  

The numbers in the files integers-32 have the wrong range, some don't fit into `int`. And there are no small numbers (like 1 or 2).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-12 22:51:57 UTC  
> Url: https://github.com/boostorg/json/issues/77#issuecomment-673148357  

"integers-64 should not exclude the 2^32 range because that's stupid"

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-01 23:03:51 UTC  
> Url: https://github.com/boostorg/json/issues/77#issuecomment-685179048  

This is no longer relevant, as we have changed the test files
