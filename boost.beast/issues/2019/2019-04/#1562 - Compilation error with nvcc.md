# #1562 - Compilation error with nvcc [Closed]

> Username: NAThompson  
> Created at: 2019-04-07 16:58:54 UTC  
> Updated at: 2019-07-30 23:00:50 UTC  
> Closed at: 2019-05-26 06:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1562  

### Version of Beast  
  
commit b701d72ddbed3b6cd39ea038e67c8dbf2e8e4e24  
  
### Steps necessary to reproduce the problem  
  
```cpp  
#include <iostream>  
#include <boost/beast.hpp>  
  
  
int main()  
{  
    std::cout << "Hello, world!\n";  
}  
```  
### All relevant compiler information  
  
```  
$ nvcc --version  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2017 NVIDIA Corporation  
Built on Fri_Nov__3_21:07:56_CDT_2017  
Cuda compilation tools, release 9.1, V9.1.85  
$ nvcc -O0 --std=c++14 -g -Xcompiler=-Wfatal-errors -I./include -I../boost  main.cu -o runserver  
```  
  
Build error:  
  
```bash  
boost/beast/core/impl/error.ipp(20): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::beast::detail::error_codes"  
  
beast/core/impl/error.ipp(55): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::beast::detail::error_conditions"  
  
boost/beast/core/impl/error.ipp(84): warning: invalid narrowing conversion from "unsigned int" to "int"  
  
boost/beast/core/impl/error.ipp(92): warning: invalid narrowing conversion from "unsigned int" to "int"  
  
boost/asio/impl/error.ipp(32): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::asio::error::detail::netdb_category"  
  
boost/asio/impl/error.ipp(64): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::asio::error::detail::addrinfo_category"  
  
boost/asio/impl/error.ipp(94): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::asio::error::detail::misc_category"  
  
boost/beast/http/impl/error.ipp(21): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::beast::http::detail::http_error_category"  
  
boost/beast/http/impl/error.ipp(96): warning: invalid narrowing conversion from "unsigned int" to "int"  
  
boost/beast/websocket/impl/error.ipp(20): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::beast::websocket::detail::error_codes"  
  
boost/beast/websocket/impl/error.ipp(117): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::beast::websocket::detail::error_conditions"  
  
boost/beast/websocket/impl/error.ipp(144): warning: invalid narrowing conversion from "unsigned int" to "int"  
  
boost/beast/websocket/impl/error.ipp(152): warning: invalid narrowing conversion from "unsigned int" to "int"  
  
boost/beast/zlib/impl/error.ipp(49): warning: overloaded virtual function "boost::system::error_category::message" is only partially overridden in class "boost::beast::zlib::detail::error_codes"  
  
boost/beast/zlib/impl/error.ipp(115): warning: invalid narrowing conversion from "unsigned int" to "int"  
  
boost/beast/http/message.hpp(265): error: static assertion failed with "Fields type requirements not met"  
          detected during:  
            instantiation of class "boost::beast::http::header<false, Fields> [with Fields=boost::beast::http::fields]"  
(495): here  
            instantiation of class "boost::beast::http::message<isRequest, Body, Fields> [with isRequest=false, Body=boost::beast::http::string_body, Fields=boost::beast::http::fields]"  
boost/beast/websocket/detail/impl_base.hpp(199): here  
  
boost/beast/http/message.hpp(61): error: static assertion failed with "Fields type requirements not met"  
          detected during:  
            instantiation of class "boost::beast::http::header<true, Fields> [with Fields=boost::beast::http::fields]"  
(495): here  
            instantiation of class "boost::beast::http::message<isRequest, Body, Fields> [with isRequest=true, Body=boost::beast::http::empty_body, Fields=boost::beast::http::fields]"  
boost/beast/websocket/detail/impl_base.hpp(257): here  
  
2 errors detected in the compilation of "/tmp/tmpxft_00002a42_00000000-6_main.cpp1.ii".  
```  
  
Ubuntu 18.04/CUDA 390, if relevant.  
  
Note: I also tried to remove the static asserts, just to see if the compile would go through. No dice:  
  
```  
boost/beast/core/detail/variant.hpp:48:41: error: request for member ‘~type’ in ‘(const boost::asio::const_buffer*&)(&((boost::beast::detail::variant<const boost::asio::const_buffer*, const boost::asio::const_buffer*, const boost::asio::const_buffer*, boost::beast::detail::buffers_cat_view_iterator_base::past_end>::destroy*)this)->boost::beast::detail::variant<const boost::asio::const_buffer*, const boost::asio::const_buffer*, const boost::asio::const_buffer*, boost::beast::detail::buffers_cat_view_iterator_base::past_end>::destroy::self.boost::beast::detail::variant<const boost::asio::const_buffer*, const boost::asio::const_buffer*, const boost::asio::const_buffer*, boost::beast::detail::buffers_cat_view_iterator_base::past_end>::buf_)’, which is of pointer type ‘T {aka const boost::asio::const_buffer*}’ (maybe you meant to use ‘->’ ?)  
             reinterpret_cast<T&>(self.buf_).~T();  
             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~  
```  
  
Also, changing the file suffix to `.cpp` fixes this problem. No clue why, since host code should be forwarded to the host compiler.

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-07 20:15:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-480625252  

Does nvcc treat `.ipp` files in a special way?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-04-07 22:17:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-480634647  

Curious why MSVC isn't giving the narrowing warning, since it has the same rules, and we have all warnings enabled.

---

## Comment 3

> Username: NAThompson  
> Created at: 2019-04-08 13:51:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-480840382  

I got some feedback on SO for this; not sure if it gets us closer to resolution:  
  
https://stackoverflow.com/a/55574913/904050

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-04-19 01:25:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-484739052  

Is this issue actionable? I'm not sure what I can do here.

---

## Comment 5

> Username: NAThompson  
> Created at: 2019-04-19 03:48:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-484758988  

@vinniefalco : Could you leave the issue open? I'm gonna submit this as a bug to NVIDIA and reference this.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-04-19 03:50:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-484759282  

If we could somehow test this configuration on Travis or Appveyor, then we can ensure any future breakages are detected early.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-05-19 04:42:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-493726597  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-05-26 06:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-495973090  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 9

> Username: izaid  
> Created at: 2019-07-30 18:38:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-516542713  

I just come across this issue myself, am figuring out how to work around it.  
  
> If we could somehow test this configuration on Travis or Appveyor, then we can ensure any future breakages are detected early.  
  
This is actually pretty straightforward. While CUDA code can't run on Travis CI, it is no problem to install NVCC on a Linux build and simply compile with it. If it compiles, Travis CI passes. I've done that before.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-07-30 20:34:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-516585031  

Would you like to submit a pull request?

---

## Comment 11

> Username: izaid  
> Created at: 2019-07-30 23:00:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1562#issuecomment-516627576  

Would it be helpful? I actually think that, unless you have access to a CUDA box, making beast consistently work with nvcc would be a large pain. My solution to this was basically to ensure beast was in a file that didn't pass through nvcc.  
  
If I get some time, I'll try and hunt down the original issue and submit at PR for that.
