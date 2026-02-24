# #132 - [MoreExamples.Variadic] Overly sparse explanation of compress [Closed]

> Username: ldionne  
> Created at: 2016-03-11 15:30:44 UTC  
> Updated at: 2016-04-28 03:29:24 UTC  
> Closed at: 2016-04-28 03:29:24 UTC  
> Url: https://github.com/boostorg/hof/issues/132  

In the `Variadic parameters` subsection of the `More examples` section of the documentation, it says  
  
> We can also take binary functions and turn them easily into varidiac functions as well using compress. So a varidiac max function could be written like this:  
>   
> ``` c++  
> FIT_STATIC_FUNCTION(max) = compress(FIT_LIFT(std::max));  
> ```  
  
This is the only thing said about `compress` in that section. Since `compress` is not something simple (you and I are used to folds, but most people are not), I would suggest quickly explaining that `compress` reduces a sequence using a binary operation, which is a bit like to `std::accumulate`. Or something like that.  
  
I understand that you must not get into too much detail, because that is the job of the reference, but saying so little is like saying nothing IMO.

---

## Comment 1

> Username: pfultz2  
> Created at: 2016-04-28 03:29:24 UTC  
> Url: https://github.com/boostorg/hof/issues/132#issuecomment-215300222  

This section has been completely rewritten with further explanation.
