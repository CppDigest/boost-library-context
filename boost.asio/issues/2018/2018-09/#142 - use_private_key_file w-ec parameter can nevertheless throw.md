# #142 - use_private_key_file w/ec parameter can nevertheless throw [Closed]

> Username: badfd  
> Created at: 2018-09-04 19:05:08 UTC  
> Updated at: 2020-12-30 00:52:55 UTC  
> Closed at: 2020-12-30 00:52:54 UTC  
> Url: https://github.com/boostorg/asio/issues/142  

There are two forms of the method: [ssl::context::use_private_key_file()](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/ssl__context/use_private_key_file.html). The first form does not take a **boost::system::error_code** parameter and the documentation says that the method throws. The second form of the method does take a **boost::system::error_code** parameter, and the documentation says that the method does not throw. [Or, at least, the documentation does not say that this second form throws.]  
  
In fact, however, the second form can throw if the password callback [see [ssl::context::set_password_callback](https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/ssl__context/set_password_callback.html)] throws -- for example, due to an excessively long password.  
  
SOLUTION: Update the documentation to reflect that the second form of the method may throw, depending on the callback. Perhaps a note to this effect, under **_Remarks_**.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:52:53 UTC  
> Url: https://github.com/boostorg/asio/issues/142#issuecomment-752289840  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#636](https://github.com/chriskohlhoff/asio/issues/636).
