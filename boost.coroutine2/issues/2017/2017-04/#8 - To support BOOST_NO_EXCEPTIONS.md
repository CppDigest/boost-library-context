# #8 - To support BOOST_NO_EXCEPTIONS [Closed]

> Username: asyncwise  
> Created at: 2017-04-11 05:47:03 UTC  
> Updated at: 2017-04-11 06:18:38 UTC  
> Closed at: 2017-04-11 06:18:15 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/8  

In the environment disabled of exception, the errors are occured like below:  
```  
error: cannot use 'try/throw' with exceptions disabled  
```

---

## Comment 1

> Username: asyncwise  
> Created at: 2017-04-11 06:18:15 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/8#issuecomment-293161888  

It looks impossible now because of other libraries using exception.

---

## Comment 2

> Username: olk  
> Created at: 2017-04-11 06:18:38 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/8#issuecomment-293161959  

exceptions are required to unwind and destruct the stack
