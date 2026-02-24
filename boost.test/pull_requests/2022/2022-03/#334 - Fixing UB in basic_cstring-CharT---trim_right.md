# #334 Fixing UB in basic_cstring<CharT>::trim_right [Merged]

> Username: raffienficiaud  
> Created at: 2022-03-05 15:58:15 UTC  
> Updated at: 2022-03-05 20:38:32 UTC  
> Merged at: 2022-03-05 20:38:28 UTC  
> Closed at: 2022-03-05 20:38:28 UTC  
> Url: https://github.com/boostorg/test/pull/334  

Rewriting the loop as suggested to stay within the valid range  
boundaries.  
  
Closes #284

---
