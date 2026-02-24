# #46 - Encapsulate code that computes 1d index from Nd index into separate class [Closed]

> Username: HDembinski  
> Created at: 2018-06-19 07:17:47 UTC  
> Updated at: 2018-07-02 09:13:15 UTC  
> Closed at: 2018-07-02 09:13:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/46  

This code is used by the iterators and inside the histogram. Putting it into a separate class will make this code separately testable as well.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-07-02 09:13:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/46#issuecomment-401726539  

Nevermind, I came to the conclusion that is better to iterate over the linear index and only decode it into a multi-dimensional index if the user asks for it.
