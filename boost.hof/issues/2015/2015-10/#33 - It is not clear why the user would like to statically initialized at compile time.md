# #33 - It is not clear why the user would like to statically initialized at compile time [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:21:12 UTC  
> Updated at: 2017-01-31 21:38:04 UTC  
> Closed at: 2017-01-31 21:38:04 UTC  
> Url: https://github.com/boostorg/hof/issues/33  

I would suggest first how the user can use the library when it doesn't need to statically initialize at compile time and only then show when it is useful to do it, the current limitation of the language C++11/C++14 and how Fit solves the issue.

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:32:27 UTC  
> Url: https://github.com/boostorg/hof/issues/33#issuecomment-145427736  

From the docs:  
  
> However, we want to statically initialize it(ie initialize it at compile-time) to avoid the static initialization order fiasco.  
  
Static initialization is only required for global function objects because the static initialization order fiasco only affects global variables.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:45:30 UTC  
> Url: https://github.com/boostorg/hof/issues/33#issuecomment-145690326  

I understand the problem of initialization,but starting with examples that solve this problem seems not good for a tutorial and less yet for a quick start.

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-30 22:57:50 UTC  
> Url: https://github.com/boostorg/hof/issues/33#issuecomment-152669667  

It is trying to show the best practice pattern for initialing global function objects. The user should always initialize them that way. If the user wants to understand why, they can read up on static initializaion order fiasco.

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-31 00:34:37 UTC  
> Url: https://github.com/boostorg/hof/issues/33#issuecomment-152680028  

I was just a suggestion. The reading of the introductory material is hard with all this specific cases.

---

## Comment 5

> Username: pfultz2  
> Created at: 2017-01-31 21:38:04 UTC  
> Url: https://github.com/boostorg/hof/issues/33#issuecomment-276500766  

Closing this for now, since the documentation has been restructured.
