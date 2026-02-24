# #6 - What happened with symmetric coroutine? [Closed]

> Username: bog-dan-ro  
> Created at: 2016-12-04 10:02:16 UTC  
> Updated at: 2016-12-04 10:14:50 UTC  
> Closed at: 2016-12-04 10:14:24 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/6  

Is there any reason why they are not part of Couroutine2?

---

## Comment 1

> Username: olk  
> Created at: 2016-12-04 10:14:24 UTC  
> Updated at: 2016-12-04 10:14:50 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/6#issuecomment-264695188  

coroutine2 supports only asymmetric coroutines - the symmetric version has had problems with its API.  
you could use execution_context (boost.context) in order to provide symmetric context switching
