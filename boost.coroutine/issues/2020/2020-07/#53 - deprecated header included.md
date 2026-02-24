# #53 - deprecated header included [Closed]

> Username: vinniefalco  
> Created at: 2020-07-29 23:34:06 UTC  
> Updated at: 2020-08-11 07:34:47 UTC  
> Closed at: 2020-08-11 07:34:47 UTC  
> Url: https://github.com/boostorg/coroutine/issues/53  

The file `<boost/detail/scoped_enum_emulation.hpp>` is being included here:  
https://github.com/boostorg/coroutine/blob/8df277dc820701e6a62f5ae4018e7b7190490eb8/include/boost/coroutine/exceptions.hpp#L14  
  
The problem is that this file is deprecated and causes warnings for any Beast or Asio users who use Asio's coroutines. Could we perhaps fix this for the release?

---

## Comment 1

> Username: olk  
> Created at: 2020-07-30 04:31:01 UTC  
> Updated at: 2020-07-30 06:56:13 UTC  
> Url: https://github.com/boostorg/coroutine/issues/53#issuecomment-666098224  

Is a pre-C++11 equivalent in boost available?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-07-30 16:14:33 UTC  
> Url: https://github.com/boostorg/coroutine/issues/53#issuecomment-666498672  

Yes, here:  
https://github.com/boostorg/core/blob/develop/include/boost/core/scoped_enum.hpp
