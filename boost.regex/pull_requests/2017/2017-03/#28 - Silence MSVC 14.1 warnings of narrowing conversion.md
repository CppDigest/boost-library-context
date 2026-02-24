# #28 Silence MSVC 14.1 warnings of narrowing conversion [Merged]

> Username: Lastique  
> Created at: 2017-03-25 17:35:20 UTC  
> Updated at: 2017-06-02 07:51:51 UTC  
> Merged at: 2017-06-02 07:16:10 UTC  
> Closed at: 2017-06-02 07:16:10 UTC  
> Url: https://github.com/boostorg/regex/pull/28  

In debug mode the compiler is not able to see that the int constant can fit in the character type without loss.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2017-05-25 13:16:00 UTC  
> Url: https://github.com/boostorg/regex/pull/28#issuecomment-304007398  

This warning spams the build output because of the huge template instatiation backtraces.

---
