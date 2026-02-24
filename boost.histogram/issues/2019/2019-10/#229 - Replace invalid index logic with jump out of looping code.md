# #229 - Replace invalid index logic with jump out of looping code? [Closed]

> Username: HDembinski  
> Created at: 2019-10-01 10:43:39 UTC  
> Updated at: 2019-10-11 21:39:14 UTC  
> Closed at: 2019-10-11 21:39:04 UTC  
> Url: https://github.com/boostorg/histogram/issues/229  

With a goto, we can jump out of the axis loop immediately, even if the loop is static.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-11 21:39:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/229#issuecomment-541233042  

Nope, does not really work.
