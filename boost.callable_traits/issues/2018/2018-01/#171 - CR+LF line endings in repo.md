# #171 - CR+LF line endings in repo [Closed]

> Username: pdimov  
> Created at: 2018-01-18 19:01:34 UTC  
> Updated at: 2018-01-20 01:42:35 UTC  
> Closed at: 2018-01-20 01:42:35 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/171  

After a fresh checkout on Linux, several files in libs/callable_traits appear modified, and there's no way to un-modify them because (I suspect) they have Windows line endings in the repository. One is `test/class_of.cpp`, there are several others.

---

## Comment 1

> Username: badair  
> Created at: 2018-01-20 01:42:35 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/171#issuecomment-359134773  

Fixed via #170, thanks @Lastique
