# #1157 - Build error for advanced server example, Visual Studio 2017 [Closed]

> Username: jostell  
> Created at: 2018-06-10 15:17:25 UTC  
> Updated at: 2018-08-10 16:18:17 UTC  
> Closed at: 2018-08-10 16:18:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1157  

Hi,  
  
I'm trying to get started with a beast project and have copied the advanced server example verbatim from the [documentation](beast/example/advanced/server/advanced_server.cpp) to get started. I've added the standard `#include "stdafx.h"` on top, everything else is identical. I get a build error in Visual Studio 2017 (version 15.7.3):  
  
> 1>------ Build started: Project: cppweb, Configuration: Debug Win32 ------  
1>cppweb.cpp  
1>c:\dev\cppweb\cppweb.cpp(516): error C2530: 'self_': references must be initialized  
1>c:\dev\cppweb\cppweb.cpp(516): note: This diagnostic occurred in the compiler generated function 'void http_session::queue::operator ()(boost::beast::http::message<isRequest,Body,Fields> &&)'  
1>Done building project "cppweb.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
  
The build error doesn't make sense to me, `self_` is initialized in the constructor. Any ideas?   
  
### Version of Beast  
  
167  
  
### Steps necessary to reproduce the problem  
  
Build advanced server example in Visual Studio 2017.  
  
### All relevant compiler information  
  
Visual Studio 2017 version 15.7.3.  
Boost 1.67.0 installed via NuGet.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-10 16:15:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-396061288  

No idea, this all compiles for me. I have never seen such an error. Try renaming `work_impl::self_`:  
```  
            // This holds a work item  
            struct work_impl : work  
            {  
                http_session& this_;  
                http::message<isRequest, Body, Fields> msg_;  
  
                work_impl(  
                    http_session& self,  
                    http::message<isRequest, Body, Fields>&& msg)  
                    : this_(self)  
                    , msg_(std::move(msg))  
                {  
                }  
  
                void  
                operator()()  
                {  
                    http::async_write(  
                        this_.socket_,  
                        msg_,  
                        boost::asio::bind_executor(  
                            this_.strand_,  
                            std::bind(  
                                &http_session::on_write,  
                                this_.shared_from_this(),  
                                std::placeholders::_1,  
                                msg_.need_eof())));  
                }  
            };  
```

---

## Comment 2

> Username: jostell  
> Created at: 2018-06-10 17:12:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-396065421  

Same error.  
  
> 1>------ Build started: Project: cppweb, Configuration: Debug Win32 ------  
1>cppweb.cpp  
1>c:\dev\cppweb\cppweb.cpp(516): error C2530: 'this_': references must be initialized  
1>c:\dev\cppweb\cppweb.cpp(516): note: This diagnostic occurred in the compiler generated function 'void http_session::queue::operator ()(boost::beast::http::message<isRequest,Body,Fields> &&)'  
1>Done building project "cppweb.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========

---

## Comment 3

> Username: jostell  
> Created at: 2018-06-10 17:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-396068373  

Making `this_` a pointer instead of a reference seems to make it compile. Compiler bug?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-06-11 01:37:13 UTC  
> Updated at: 2018-06-11 01:37:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-396100151  

> Compiler bug?  
  
Definitely. Are you on the latest Visual Studio?

---

## Comment 5

> Username: jostell  
> Created at: 2018-06-11 08:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-396160544  

Yes, 15.7.3 is the latest release.  
  
Looks like a [problem with the conformance mode flag](https://developercommunity.visualstudio.com/content/problem/258362/another-permissive-problem-on-msvc-1914264281.html), `/permissive-`, which is enabled by default for new projects since 15.5.   
  
I can confirm that disabling conformance mode does the trick.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-06-11 13:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-396241067  

I forgot why I turned off `/permissive-`:  
https://github.com/boostorg/beast/blob/93c35524a6125db3e6aeefc8abc826a810dd5d61/CMakeLists.txt#L27

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-07-11 13:32:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-404170396  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-07-11 14:18:27 UTC  
> Updated at: 2018-07-11 14:18:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-404186335  

I am investigating why Boost.Range produces a compilation error with **/permissive-**

---

## Comment 9

> Username: stale[bot]  
> Created at: 2018-08-10 14:28:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-412099244  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-08-10 16:18:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1157#issuecomment-412132517  

This issue has been resolved.
