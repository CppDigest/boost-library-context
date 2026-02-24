# #189 - Remove the newly introduced dependency cycle [Closed]

> Username: glenfe  
> Created at: 2021-10-15 22:20:29 UTC  
> Updated at: 2021-10-16 06:56:59 UTC  
> Closed at: 2021-10-16 06:56:49 UTC  
> Url: https://github.com/boostorg/context/issues/189  

Currently as of 520128fab2cfc0ab7b7f52d6eb218d88b705546c we have  Asio -> Coroutine -> Context -> Asio. We should remove this cycle before we release 1.78.0.

---

## Comment 1

> Username: olk  
> Created at: 2021-10-16 06:56:49 UTC  
> Updated at: 2021-10-16 06:56:59 UTC  
> Url: https://github.com/boostorg/context/issues/189#issuecomment-944869681  

reverted
