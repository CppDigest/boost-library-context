# #72 - reveal - add an example of compiler error with and without the use of reveal. [Closed]

> Username: viboes  
> Created at: 2015-10-04 16:35:15 UTC  
> Updated at: 2016-02-23 01:35:23 UTC  
> Closed at: 2016-02-23 01:35:23 UTC  
> Url: https://github.com/boostorg/hof/issues/72  



---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-25 23:39:41 UTC  
> Url: https://github.com/boostorg/hof/issues/72#issuecomment-150987812  

Well, I show the errors [here](http://pfultz2.com/blog/2015/02/27/error-messages/), except that example relies on the Tick library. I would like to get the patch in for clang before showing the error messages, as the patch helps improve error reporting a lot.

---

## Comment 2

> Username: pfultz2  
> Created at: 2015-10-25 23:42:55 UTC  
> Url: https://github.com/boostorg/hof/issues/72#issuecomment-150988299  

The review for the patch on clang is [here](http://reviews.llvm.org/D8309).

---

## Comment 3

> Username: tzlaine  
> Created at: 2016-02-09 15:00:45 UTC  
> Url: https://github.com/boostorg/hof/issues/72#issuecomment-181904407  

+1 to quoting error messages in your docs, even if they change in Clang later.

---

## Comment 4

> Username: pfultz2  
> Created at: 2016-02-23 01:35:23 UTC  
> Url: https://github.com/boostorg/hof/issues/72#issuecomment-187466414  

So I added a sample of the error message as well as a some more info about doing custom failure reporting with the `reveal` adaptor. I'll update it later with the error messages from the new Clang.
