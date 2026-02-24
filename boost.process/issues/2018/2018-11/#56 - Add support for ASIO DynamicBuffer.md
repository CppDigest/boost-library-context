# #56 - Add support for ASIO DynamicBuffer [Open]

> Username: reddwarf69  
> Created at: 2018-11-21 12:41:57 UTC  
> Updated at: 2019-04-10 04:52:44 UTC  
> Url: https://github.com/boostorg/process/issues/56  

https://github.com/boostorg/process/blob/develop/include/boost/process/io.hpp#L241 is very handy, but it would be even more handy if there would be an overload supporting a DynamicBuffer (https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/DynamicBuffer.html) as argument.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-11-21 15:50:13 UTC  
> Url: https://github.com/boostorg/process/issues/56#issuecomment-440714549  

Any idea on how to do the overload? If you pass in anything that is a `mutable_buffer` it works, but I don't have concepts for C++11, so I need the type, e.g. `streambuf`. If you have an idea, I'll be glad to add it.

---

## Comment 2

> Username: reddwarf69  
> Created at: 2018-11-21 16:23:58 UTC  
> Url: https://github.com/boostorg/process/issues/56#issuecomment-440726518  

Well, I'm basically a C++ newbie, so take anything I say with a grain of salt. But I guess it's just about using SFINAE. For example, see https://github.com/boostorg/asio/blob/develop/include/boost/asio/impl/read.hpp#L662, which has lots of overloads (https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/async_read.html)  
  
I don't see it in the docs, but given that it's not under a `detail` namespace I guess you are free to use the is_dynamic_buffer definition from ASIO (https://github.com/boostorg/asio/blob/develop/include/boost/asio/buffer.hpp#L2159)

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-11-22 12:38:06 UTC  
> Url: https://github.com/boostorg/process/issues/56#issuecomment-441018118  

`SFINAE` might work, will try it when I get to work on the library again.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2019-04-10 04:52:44 UTC  
> Url: https://github.com/boostorg/process/issues/56#issuecomment-481532815  

@RedDwarf69 I added a prototype for SFINAE, can you try if that works for you?
