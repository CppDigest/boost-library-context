# #87 Modify docs Jamfile [Closed]

> Username: sdarwin  
> Created at: 2021-12-08 21:09:36 UTC  
> Updated at: 2021-12-09 00:08:09 UTC  
> Closed at: 2021-12-09 00:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/87  

As a point of comparison, this update seems to help fix the problem.  
  
@Lastique wrote: "Don't do unnecessary glob."  
  
Yet, simply adding the glob, changed the pages from being incorrect, to working again.  It's based on the example from boostorg/core.    How should this be changed..

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-12-08 21:16:19 UTC  
> Url: https://github.com/boostorg/utility/pull/87#issuecomment-989202348  

An automated preview of the documentation is available at [https://87.utility.prtest.cppalliance.org/libs/utility/index.html](https://87.utility.prtest.cppalliance.org/libs/utility/index.html)

---

## Comment 2

> Username: Lastique  
> Created_at: 2021-12-08 22:36:36 UTC  
> Url: https://github.com/boostorg/utility/pull/87#issuecomment-989283419  

Oh, sorry, I didn't notice this PR before I committed my version.  
  
The fact that `glob` seems to fix this is coincidental, I think. It's because it returns an absolute path, although I don't think it is documented to do so. In any case, using `glob` to just get an absolute path doesn't look efficient to me.  
  
Let's see if `path-constant` works.

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2021-12-08 22:51:43 UTC  
> Url: https://github.com/boostorg/utility/pull/87#issuecomment-989291464  

That's why the last PR worked for me and not @Lastique then! 😮  
  
Boost.core is doing the same, but this `glob` does look bad. But we if we are manually patching www.boost.org/doc/libs/1_78_0/libs/utility/ manually, this output should be just as good for that.  
  
I think all solutions will be workarounds and guesses, like boost.core, until a b2 expert looks into that. Do we have a b2 command to call cmake yet? 😆

---

## Comment 4

> Username: Lastique  
> Created_at: 2021-12-09 00:08:09 UTC  
> Url: https://github.com/boostorg/utility/pull/87#issuecomment-989328674  

`path-constant` seems to be working, so I'll close this PR. Thanks.

---
