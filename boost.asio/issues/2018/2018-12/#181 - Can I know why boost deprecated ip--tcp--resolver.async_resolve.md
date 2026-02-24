# #181 - Can I know why boost deprecated ip::tcp::resolver.async_resolve? [Closed]

> Username: JohnHe404  
> Created at: 2018-12-23 14:52:34 UTC  
> Updated at: 2019-01-17 05:22:43 UTC  
> Closed at: 2019-01-17 05:22:43 UTC  
> Url: https://github.com/boostorg/asio/issues/181  

I read last [doc](  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/ip__tcp/resolver.html).  
async_resolve already deprecated.  
  
In this [doc](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/n4771.pdf),  
async_resolve is not deprecated.  
  
  
Can I know why boost deprecated ip::tcp::resolver.async_resolve?  
  
~~

---

## Comment 1

> Username: djarek  
> Created at: 2018-12-24 11:08:23 UTC  
> Url: https://github.com/boostorg/asio/issues/181#issuecomment-449720239  

Unfortunately that's just misleading in the documentation, because it's only the 1st overload of `async_resolve` that is deprecated: https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/ip__basic_resolver/async_resolve.html

---

## Comment 2

> Username: JohnHe404  
> Created at: 2018-12-24 14:24:50 UTC  
> Url: https://github.com/boostorg/asio/issues/181#issuecomment-449740071  

@djarek  Thanks a million.
