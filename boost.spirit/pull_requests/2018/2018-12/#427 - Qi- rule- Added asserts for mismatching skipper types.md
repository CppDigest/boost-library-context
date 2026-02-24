# #427 Qi: rule: Added asserts for mismatching skipper types [Merged]

> Username: Kojoley  
> Created at: 2018-12-17 22:28:34 UTC  
> Updated at: 2018-12-18 17:23:56 UTC  
> Merged at: 2018-12-18 12:48:11 UTC  
> Closed at: 2018-12-18 12:48:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/427  

It looks like mismatching skipper types and `parse`/`phrase_parse` calls are  
quite popular mistakes, and the actual problem is not clear for users despite  
that there is a comment near the call that will fail and the hint should be  
found by following to the file and line that compiler reports the error on.  
  
Closes #426.

---

## Comment 1

> Username: mwpowellhtx  
> Created_at: 2018-12-18 17:23:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/427#issuecomment-448300856  

I'm not sure what you mean:  
  
> ... mismatching skipper types ...  
  
That's not what's going on at all. I tried furnishing a skipper instance during my troubleshooting of the issue, yes; but that is by no means the root cause.

---
