# #32 - Can thread specific storage be used together with boost context? [Closed]

> Username: gdochev  
> Created at: 2016-06-06 13:55:24 UTC  
> Updated at: 2016-06-06 14:40:49 UTC  
> Closed at: 2016-06-06 14:00:45 UTC  
> Url: https://github.com/boostorg/context/issues/32  

I am testing on OSX using the simplified code below. From the main thread:  
  
```  
    int rc = pthread_key_create(&tlsKey, nullptr);  
    assert(!rc);  
```  
  
From another thread:  
  
```  
  
    int rc = pthread_setspecific(tlsKey, (void*)0x12345678);  
    assert(!rc);  
    assert((void*)0x12345678 == pthread_getspecific(tlsKey)); // OK  
  
    // code that switches contexts using jump_fcontext back and forth  
    ...  
  
    assert((void*)0x12345678 == pthread_getspecific(tlsKey);  // FAILURE, returns NULL  
  
```

---

## Comment 1

> Username: olk  
> Created at: 2016-06-06 14:00:45 UTC  
> Url: https://github.com/boostorg/context/issues/32#issuecomment-223967520  

You can use TLS together with boost.context as long as you do not migrate an execution_context, that uses TLS, from one thread to another.

---

## Comment 2

> Username: gdochev  
> Created at: 2016-06-06 14:06:25 UTC  
> Url: https://github.com/boostorg/context/issues/32#issuecomment-223969143  

Thanks for the quick response! Can you elaborate what you mean by "as long as you do not migrate an execution_context, that uses TLS, from one thread to another." If you mean, storing execution_context inside TLS I do not do that.

---

## Comment 3

> Username: olk  
> Created at: 2016-06-06 14:12:51 UTC  
> Url: https://github.com/boostorg/context/issues/32#issuecomment-223970884  

migrating execution_context to another thread == an execution_context is suspended in thread A and resumed in thread B

---

## Comment 4

> Username: gdochev  
> Created at: 2016-06-06 14:40:49 UTC  
> Url: https://github.com/boostorg/context/issues/32#issuecomment-223979304  

I do do that but the execution_context always ends up in the same thread A, as in the code above where I call pthread_getspecific from the same thread from which I set it with pthread_setspecific.
