# #344 - ?= not explained in the documentation. [Closed]

> Username: ShimmerFairy  
> Created at: 2018-09-30 06:17:47 UTC  
> Updated at: 2019-03-26 04:38:42 UTC  
> Closed at: 2019-03-26 04:38:42 UTC  
> Url: https://github.com/boostorg/build/issues/344  

While I have a guess as to what it means (assigns to the variable if it's empty), I want to check the documentation to make sure. However, [section 11.2.6 of the online documentation](https://boostorg.github.io/build/manual/develop/index.html#jam.language.variables) shows off that `?=` exists without ever explaining it. In fact, `?=` doesn't show up anywhere else in the entire manual, much less get explained.  
  
Right now I'm trying to look at the code of boost build itself to try and find out what the answer is, but it's not that easy to find.

---

## Comment 1

> Username: grisumbras  
> Created at: 2018-09-30 11:15:16 UTC  
> Url: https://github.com/boostorg/build/issues/344#issuecomment-425713329  

The section does explain what it does in the very paragraph that follows that snippet  
  
>The final two forms are synonymous: they set variable globally, but only if it was previously unset.
