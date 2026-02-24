# #2606 - completion-less spawn is deprecated. [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-24 02:53:41 UTC  
> Updated at: 2023-02-02 15:27:52 UTC  
> Closed at: 2023-02-02 15:27:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2606  

The stackful coroutine examples need to be updated to handle the case where the spawned function exits via exception. This needs to be commented so the user understands. And we need to define the Asio macro which turns use of deprecated functions into a compile error.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-24 02:54:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2606#issuecomment-1364447093  

The stackful coroutine examples need to be updated to handle the case where the spawned function exits via exception. This needs to be commented so the user understands. And we need to define the Asio macro which turns use of deprecated functions into a compile error.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-12-24 03:09:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2606#issuecomment-1364449047  

This will need a bit more work, since it'll also change the signatures in the tests & the way stack are allocated with boost.context. `boost::coroutine::attributes` is deprecated as well.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-12-24 03:12:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2606#issuecomment-1364449450  

derp
