# #1916 - Use BOOST_ASIO_COMPLETION_TOKEN_FOR [Closed]

> Username: Vilsol  
> Created at: 2020-04-24 20:38:27 UTC  
> Updated at: 2020-05-01 05:16:33 UTC  
> Closed at: 2020-05-01 05:16:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1916  

I am trying to create a basic websocket server, everything seemed to be compiling fine, but now I have encountered this error and I have no idea how to solve it:  
  
```  
***/beast/websocket/impl/accept.hpp(603): error C2244: 'boost::beast::websocket::stream<NextLayer,deflateSupported>::async_accept': unable to match function definition to an existing declaration  
***/beast/websocket/impl/accept.hpp(596): note: see declaration of 'boost::beast::websocket::stream<NextLayer,deflateSupported>::async_accept'  
***/beast/websocket/impl/accept.hpp(603): note: definition  
***/beast/websocket/impl/accept.hpp(603): note: 'auto boost::beast::websocket::stream<NextLayer,deflateSupported>::async_accept(const ConstBufferSequence &,AcceptHandler &&,std::enable_if<!boost::beast::http::detail::is_header<ConstBufferSequence>::value,void>::type *)'  
***/beast/websocket/impl/accept.hpp(603): note: existing declarations  
***/beast/websocket/impl/accept.hpp(603): note: 'auto boost::beast::websocket::stream<NextLayer,deflateSupported>::async_accept(const boost::beast::http::message<true,Body,Fields> &,AcceptHandler &&)'  
        with  
        [  
            Fields=boost::beast::http::basic_fields<Allocator>  
        ]  
***/beast/websocket/impl/accept.hpp(603): note: 'auto boost::beast::websocket::stream<NextLayer,deflateSupported>::async_accept(const ConstBufferSequence &,AcceptHandler &&)'  
***/beast/websocket/impl/accept.hpp(603): note: 'auto boost::beast::websocket::stream<NextLayer,deflateSupported>::async_accept(AcceptHandler &&)'  
```  
  
I am using this library in conjunction with UE4

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-04-24 20:55:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-619234880  

I have no idea how to solve it either, because you didn't include a snippet of the source code in question.

---

## Comment 2

> Username: Vilsol  
> Created at: 2020-04-24 21:10:13 UTC  
> Updated at: 2020-04-24 21:12:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-619240070  

The absolute smallest example I could come up with:  
  
```c++  
#include "./Socket.h"  
  
#include "Windows/WindowsHWrapper.h"  
  
THIRD_PARTY_INCLUDES_START  
#pragma push_macro("check")  
#undef check  
#include "Windows/AllowWindowsPlatformTypes.h"  
#include "Windows/AllowWindowsPlatformAtomics.h"  
#include <boost/beast/websocket.hpp>  
#include "Windows/HideWindowsPlatformAtomics.h"  
#include "Windows/HideWindowsPlatformTypes.h"  
#pragma pop_macro("check")  
THIRD_PARTY_INCLUDES_END  
  
namespace SDP {  
	namespace Web {  
		void handle_socket(boost::beast::http::request<boost::beast::http::string_body> req) {  
		}  
	}  
}  
```  
  
It seems that as soon as I add any reference to `#include <boost/beast/websocket.hpp>` it just breaks.  
  
I am using boost 1.72

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-04-25 01:24:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-619298290  

Do the beast examples and tests compile for you?

---

## Comment 4

> Username: Vilsol  
> Created at: 2020-04-25 01:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-619299040  

No, they do not. Which is why I made this issue. But the weird part is that only this one specific function is throwing the error. I am using other boost libraries without any issue, even the beast webserver itself, and it works just fine.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-04-25 01:34:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-619299314  

I wonder if you have two different versions of Boost installed

---

## Comment 6

> Username: Vilsol  
> Created at: 2020-04-25 03:42:17 UTC  
> Updated at: 2020-04-25 03:42:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-619314001  

I have had to explicitly specify the include directory and the used lib's. And even ignoring that, I have only installed boost once.

---

## Comment 7

> Username: manni05  
> Created at: 2020-04-29 11:25:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621141065  

I'm gettig similar errors (*) by simply compiling a file with only "#include <boost/beast/core.hpp>" as content. I am using boost 1.73 and VS 2019 Version 16.5.4 with /std:c++-latest. The problem seems to be C++20 coroutine support. The template declarations and definitions differ: the decalartions contain a macro that translates to "::boost::asio::completion_token_for", the definitions don't contain that macro. I startet to insert that macro into the definition and that seems to fix the problem.   
  
  
*)  
- error C2244: 'boost::beast::basic_stream<Protocol,Executor,RatePolicy>::async_connect': unable to match function definition to an existing declaration  
- error C2244: 'boost::beast::buffered_read_stream<Stream,DynamicBuffer>::async_write_some' ...  
- error C2244: 'boost::beast::flat_stream<NextLayer>::async_read_some'

---

## Comment 8

> Username: manni05  
> Created at: 2020-04-29 12:13:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621163182  

These changes are fixing my problem:  
  
[beast_async.txt](https://github.com/boostorg/beast/files/4551513/beast_async.txt)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-04-29 15:53:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621301486  

Interesting, so `BOOST_ASIO_COMPLETION_TOKEN_FOR` is a new Asio thing?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-04-29 15:54:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621302393  

All Beast initiating functions should use the macro `BOOST_ASIO_COMPLETION_TOKEN_FOR` for the completion token. We still need the static_assert on the handler signature for pre-c++20 toolchains.

---

## Comment 11

> Username: manni05  
> Created at: 2020-04-30 08:33:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621694266  

I am not sure whether it has become clear, but I have only included the macros that **where already used** in the function declaration into the implementation. My synchronous code is translated again. I do not know whether the asynchronous functions can actually be used with VS 2019 and /std:c++-latest.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-04-30 10:47:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621757461  

This COMPLETION_TOKEN_FOR business supercedes our own checks that static_assert token conformance with the CompletionToken NTR.  
  
We could therefore remove our own checks build environments that support concepts.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-04-30 13:22:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1916#issuecomment-621832291  

> We could therefore remove our own checks build environments that support concepts.  
  
That doesn't help us because we still need our own checks in all the other environments.
