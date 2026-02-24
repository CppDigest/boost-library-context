# #46 - unable to create not-a-coroutine [Closed]

> Username: tobias-loew  
> Created at: 2023-05-23 15:00:57 UTC  
> Updated at: 2023-10-02 07:12:00 UTC  
> Closed at: 2023-10-01 14:56:41 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/46  

With Boost.Coroutine it is possible to create a default constructed coroutine-object and get _not-a-coroutine_, which e.g. later can be swapped with a real coroutine. With Boost.Coroutine2 the default constructor is not available, and construction from a nullptr of type control_block* is private.  
Is there any specific reason for not allowing default-created coroutines?

---

## Comment 1

> Username: olk  
> Created at: 2023-10-01 14:56:41 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/46#issuecomment-1742106946  

a coroutine must be associated with an execution context (stack + registers)  
additionally boost's coroutine are created in pairs (a push- and a pull-coroutine)  
a default constructor would not make sense because it can not be associated with an execution context (and can't be later - at least with the current boost.coroutine syntax)

---

## Comment 2

> Username: tobias-loew  
> Created at: 2023-10-02 07:11:59 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/46#issuecomment-1742504251  

Thanks for the clarification.
