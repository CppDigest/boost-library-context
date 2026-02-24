# #205 - Fix asio example [Closed]

> Username: Warchant  
> Created at: 2019-05-17 09:33:14 UTC  
> Updated at: 2019-05-17 09:55:44 UTC  
> Closed at: 2019-05-17 09:55:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/205  

In this commit asio/handler_type.h is removed for asio 1.70.0:  
  
https://github.com/boostorg/asio/commit/b572bfbc217fe51b8d27292042a3db1d1ec4a9c6#diff-2fe8469ef1412ce812cc2703c24c669fL64  
  
But it is still used in asio example `autoecho` 1.70.0:  
https://github.com/boostorg/fiber/blob/develop/examples/asio/detail/yield.hpp#L11  
  
UPD: also, I am requesting new example for post 1.70.0 asio. Thanks.

---

## Comment 1

> Username: olk  
> Created at: 2019-05-17 09:55:44 UTC  
> Url: https://github.com/boostorg/fiber/issues/205#issuecomment-493396761  

duplicate of  #201
