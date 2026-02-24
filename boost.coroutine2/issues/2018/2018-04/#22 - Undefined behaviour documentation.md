# #22 - Undefined behaviour documentation [Closed]

> Username: hamparawa  
> Created at: 2018-04-20 09:27:48 UTC  
> Updated at: 2018-04-20 10:44:36 UTC  
> Closed at: 2018-04-20 10:44:24 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/22  

Hi,  
The documentation states that,  
  
> Calling coroutine<>::push_type::operator() and coroutine<>::pull_type::operator() from inside the same coroutine results in undefined behaviour.   
  
and provides the following example,  
  
```  
boost::coroutines2::coroutine<void>::push_type coro(  
    [&](boost::coroutines2::coroutine<void>::pull_type& yield){  
        yield();  
});  
coro();  
```  
  
Shouldn't this be something like this?  
  
```  
boost::coroutines2::coroutine<void>::push_type coro(  
    [&](boost::coroutines2::coroutine<void>::pull_type& yield){  
        coro();  
});  
coro();  
```

---

## Comment 1

> Username: hamparawa  
> Created at: 2018-04-20 09:28:41 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/22#issuecomment-383040667  

#21

---

## Comment 2

> Username: olk  
> Created at: 2018-04-20 10:44:24 UTC  
> Updated at: 2018-04-20 10:44:36 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/22#issuecomment-383058657  

correct, ty
