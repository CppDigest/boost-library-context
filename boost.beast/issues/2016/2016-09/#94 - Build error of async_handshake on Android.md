# #94 - Build error of async_handshake on Android [Closed]

> Username: sksinishin  
> Created at: 2016-09-26 09:00:24 UTC  
> Updated at: 2016-09-29 18:32:00 UTC  
> Closed at: 2016-09-29 18:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/94  

code:  
  
```  
typedef std::function<void(const boost::system::error_code&)> async_handler;  
  
void connection::async_connect_ws(const std::string& host, int port, async_handler handler) {  
_ws_stream.async_handshake(host, "/", [this, self, handler](const boost::system::error_code& ec)  
 {  
      if (ec)   {  
            LOG_ERR("Can't switch to websocket protocol");  
      }  
  
       if (handler)  
           handler(ec);  
 });  
}  
```  
  
**Clang + llvm stl (c++_static)**  
  
Errors:  
  
```  
In file included from src/client/connection.cpp:9:  
In file included from src/client/connection.hpp:12:  
/Users/user/Library/Android/sdk/ndk-bundle/sources/cxx-stl/llvm-libc++/libcxx/include/memory:4285:39: error: 'rebind' following the 'template' keyword does not refer to a template  
    typedef typename _Alloc::template rebind<_CntrlBlk>::other _A2;  
                                      ^~~~~~  
/Users/user/Library/Android/sdk/ndk-bundle/sources/cxx-stl/llvm-libc++/libcxx/include/memory:4642:29: note: in instantiation of function template specialization 'std::__ndk1::shared_ptr<beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &>::handshake_op<(lambda at src/client/connection.cpp:133:63)>::data>::allocate_shared<beast::handler_alloc<char, (lambda at client/connection.cpp:133:63)>, (lambda at client/connection.cpp:133:63) &, beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &> &, const boost::basic_string_ref<char, std::__ndk1::char_traits<char> > &, const boost::basic_string_ref<char, std::__ndk1::char_traits<char> > &>' requested here  
    return shared_ptr<_Tp>::allocate_shared(__a, _VSTD::forward<_Args>(__args)...);  
                            ^  
3rdparty/include/beast/websocket/impl/handshake_op.ipp:64:19: note: in instantiation of function template specialization 'std::__ndk1::allocate_shared<beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &>::handshake_op<(lambda at client/connection.cpp:133:63)>::data, beast::handler_alloc<char, (lambda at client/connection.cpp:133:63)>, (lambda at client/connection.cpp:133:63) &, beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &> &, const boost::basic_string_ref<char, std::__ndk1::char_traits<char> > &, const boost::basic_string_ref<char, std::__ndk1::char_traits<char> > &>' requested here  
        : d_(std::allocate_shared<data>(alloc_type{h},  
                  ^  
3rdparty/include/beast/websocket/impl/stream.ipp:389:5: note: in instantiation of function template specialization 'beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &>::handshake_op<(lambda at client/connection.cpp:133:63)>::handshake_op<(lambda at client/connection.cpp:133:63) &, const boost::basic_string_ref<char, std::__ndk1::char_traits<char> > &, const boost::basic_string_ref<char, std::__ndk1::char_traits<char> > &>' requested here  
    handshake_op<decltype(completion.handler)>{  
    ^  
src/client/connection.cpp:133:36: note: in instantiation of function template specialization 'beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp, boost::asio::stream_socket_service<boost::asio::ip::tcp> > &>::async_handshake<(lambda at client/connection.cpp:133:63)>' requested here  
                        _ws_stream.async_handshake(host, "/", [this, self, handler](const boost::system::error_code& ec)  
```  
  
**clang gnustl_static**  
  
Errors:  
`In file included from src/client/client.cpp:9:  
In file included from src/client/client.hpp:22:  
In file included from src/client/connection.hpp:15:  
In file included from 3rdparty/include/beast/websocket.hpp:14:  
In file included from 3rdparty/include/beast/websocket/stream.hpp:13:  
In file included from 3rdparty/include/beast/http/message_v1.hpp:133:  
3rdparty/include/beast/http/impl/message_v1.ipp:144:22: error: no member named 'to_string' in namespace 'std'  
                std::to_string(*pi.content_length));  
                ~~~~~^`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-26 11:30:00 UTC  
> Updated at: 2016-09-26 11:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249545409  

- Please try adding the following nested type to the public section of `handler_alloc` in `include/beast/core/handler_alloc.hpp` and let me know?  
  
```  
template<class U>  
struct rebind  
{  
  using other = handler_alloc<U, CompletionHandler>;  
};  
```  
- Please try adding `#include <string>` to `include/beast/http/impl/message_v1.ipp` and let me know if that fixes the error?

---

## Comment 2

> Username: sksinishin  
> Created at: 2016-09-26 11:37:32 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249546756  

No. In <string> of gnu's stl on Android is missed std::to_string(). More standard is llvm' stl, but with this i get first error. Interesting what clang+llvm's stl on iOS all fine...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-09-26 11:38:18 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249546871  

Hmm...I see what you mean. Does this StackOverflow answer help at all?  
http://stackoverflow.com/questions/22774009/android-ndk-stdto-string-support

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-09-26 11:39:03 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249547002  

I believe the first error is an actual bug, Beast's `handler_alloc` is missing the `rebind_alloc` member which is required for allocators.

---

## Comment 5

> Username: sksinishin  
> Created at: 2016-09-26 11:44:31 UTC  
> Updated at: 2016-09-26 11:45:09 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249547969  

I don't want to use gnu stl. This not complete and missing functions what i need as atomic_load<shared_ptr>, etc. I don't want do workaround for many standard functions of STL. As this not option for me

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-09-26 11:50:12 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249548880  

Okay, what about adding the `rebind` alias to `handler_alloc`? Does that fix the first error?

---

## Comment 7

> Username: sksinishin  
> Created at: 2016-09-26 11:52:49 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249549371  

I see what in iOS's implementation of STL allocate_shared implemented different from Android's :  
`shared_ptr<_Tp>::allocate_shared(const _Alloc& __a, _Args&& ...__args)  
{  
    typedef __shared_ptr_emplace<_Tp, _Alloc> _CntrlBlk;  
    typedef typename __allocator_traits_rebind<_Alloc, _CntrlBlk>::type _A2;  
    typedef __allocator_destructor<_A2> _D2;  
    _A2 __a2(__a);  
`  
  
```  
shared_ptr<_Tp>::allocate_shared(const _Alloc& __a, _Args&& ...__args)  
{  
    typedef __shared_ptr_emplace<_Tp, _Alloc> _CntrlBlk;  
    typedef typename _Alloc::template rebind<_CntrlBlk>::other _A2;  
    typedef __allocator_destructor<_A2> _D2;  
    _A2 __a2(__a);  
```

---

## Comment 8

> Username: sksinishin  
> Created at: 2016-09-26 11:53:53 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249549590  

How i can add alias of rebind to handler_alloc? Can you send me this fragment of code and will check.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2016-09-26 12:01:23 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249550909  

You can cherry-pick the commit from this branch:  
https://github.com/vinniefalco/Beast/commits/alloc  
  
Note, this is untested

---

## Comment 10

> Username: sksinishin  
> Created at: 2016-09-26 12:05:57 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249551661  

With this change this pass compile!

---

## Comment 11

> Username: sksinishin  
> Created at: 2016-09-26 12:10:43 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249552608  

When b14 released?

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-09-26 12:16:07 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-249553653  

Probably this week

---

## Comment 13

> Username: vinniefalco  
> Created at: 2016-09-29 18:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/94#issuecomment-250552280  

Fixed in b14
