# #490 - less/more clever in utf8 checking [Closed]

> Username: vinniefalco  
> Created at: 2017-06-14 22:04:04 UTC  
> Updated at: 2017-07-25 17:44:13 UTC  
> Closed at: 2017-06-15 20:37:54 UTC  
> Url: https://github.com/boostorg/beast/issues/490  

utf8 handling of the mask for detecting all 1-byte code points should be better. It should first align the pointer if it can, if it finds a high bit during alignment then shift to the normal loop which does not mask check. If the pointer gets aligned, run the fast loop which checks the mask, until the mask fails in which case shift to the normal loop as before.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-15 20:37:54 UTC  
> Url: https://github.com/boostorg/beast/issues/490#issuecomment-308859010  

In version 58
