# #196 - Not able to capture beast::http::response in lambda [Closed]

> Username: djarek  
> Created at: 2016-11-20 03:17:31 UTC  
> Updated at: 2017-01-10 23:50:35 UTC  
> Closed at: 2017-01-10 23:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/196  

Example code:  
```  
beast::http::response<beast::http::string_body> response;  
auto l = [response]() {  
};  
l();  
```  
Fails to compile due to the the message struct constructor template:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/message.hpp#L282-L292  
because the compiler treats it as the move constructor and tries to construct a std::string from a beast message. Here's the full error log (g++ 5.2.1, Ubuntu 16.04, Beast revision 79be7f8b395a2d218f2f74fda3a85a222eb61a1d):  
```  
/home/damian/projects/forgottenserver/build> make -j4  
[  1%] Generating CXX prefix source cotire/tfs_CXX_prefix.cxx  
[  2%] Generating CXX prefix header cotire/tfs_CXX_prefix.hxx  
[  3%] Building CXX precompiled header cotire/tfs_CXX_prefix.hxx.gch  
Scanning dependencies of target tfs  
[  4%] [  6%] [  7%] [  8%] Building CXX object CMakeFiles/tfs.dir/src/api_server/peer.cpp.o  
Building CXX object CMakeFiles/tfs.dir/src/actions.cpp.o  
Building CXX object CMakeFiles/tfs.dir/src/otpch.cpp.o  
Building CXX object CMakeFiles/tfs.dir/src/api_server/response_writer.cpp.o  
In file included from /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/basic_parser_v1.hpp:11:0,  
                 from /home/damian/projects/forgottenserver/ext/Beast/include/beast/http.hpp:12,  
                 from /home/damian/projects/forgottenserver/src/otpch.h:43,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.cxx:4,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.hxx:4:  
/home/damian/projects/forgottenserver/ext/Beast/include/beast/http/message.hpp: In instantiation of ‘beast::http::message<isRequest, Body, Headers>::message(U&&) [with U = beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >&; <template-parameter-2-2> = std::enable_if<false, void>; bool isRequest = false; Body = beast::http::string_body; Headers = beast::http::basic_headers<std::allocator<char> >]’:  
/home/damian/projects/forgottenserver/src/api_server/peer.cpp:163:11:   required from here  
/home/damian/projects/forgottenserver/ext/Beast/include/beast/http/message.hpp:283:34: error: no matching function for call to ‘std::__cxx11::basic_string<char>::basic_string(beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >&)’  
         : body(std::forward<U>(u))  
                                  ^  
In file included from /usr/include/c++/5/string:52:0,  
                 from /usr/include/c++/5/random:40,  
                 from /usr/include/c++/5/bits/stl_algo.h:66,  
                 from /usr/include/c++/5/algorithm:62,  
                 from /home/damian/projects/forgottenserver/src/otpch.h:25,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.cxx:4,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.hxx:4:  
/usr/include/c++/5/bits/basic_string.h:535:9: note: candidate: template<class _InputIterator, class> std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(_InputIterator, _InputIterator, const _Alloc&)  
         basic_string(_InputIterator __beg, _InputIterator __end,  
         ^  
/usr/include/c++/5/bits/basic_string.h:535:9: note:   template argument deduction/substitution failed:  
In file included from /home/damian/projects/forgottenserver/ext/Beast/include/beast/http/basic_parser_v1.hpp:11:0,  
                 from /home/damian/projects/forgottenserver/ext/Beast/include/beast/http.hpp:12,  
                 from /home/damian/projects/forgottenserver/src/otpch.h:43,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.cxx:4,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.hxx:4:  
/home/damian/projects/forgottenserver/ext/Beast/include/beast/http/message.hpp:283:34: note:   candidate expects 3 arguments, 1 provided  
         : body(std::forward<U>(u))  
                                  ^  
In file included from /usr/include/c++/5/string:52:0,  
                 from /usr/include/c++/5/random:40,  
                 from /usr/include/c++/5/bits/stl_algo.h:66,  
                 from /usr/include/c++/5/algorithm:62,  
                 from /home/damian/projects/forgottenserver/src/otpch.h:25,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.cxx:4,  
                 from /home/damian/projects/forgottenserver/build/cotire/tfs_CXX_prefix.hxx:4:  
/usr/include/c++/5/bits/basic_string.h:512:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&&, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(basic_string&& __str, const _Alloc& __a)  
       ^  
/usr/include/c++/5/bits/basic_string.h:512:7: note:   candidate expects 2 arguments, 1 provided  
/usr/include/c++/5/bits/basic_string.h:508:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(const basic_string& __str, const _Alloc& __a)  
       ^  
/usr/include/c++/5/bits/basic_string.h:508:7: note:   candidate expects 2 arguments, 1 provided  
/usr/include/c++/5/bits/basic_string.h:504:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::initializer_list<_Tp>, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(initializer_list<_CharT> __l, const _Alloc& __a = _Alloc())  
       ^  
/usr/include/c++/5/bits/basic_string.h:504:7: note:   no known conversion for argument 1 from ‘beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >’ to ‘std::initializer_list<char>’  
/usr/include/c++/5/bits/basic_string.h:477:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(basic_string&& __str) noexcept  
       ^  
/usr/include/c++/5/bits/basic_string.h:477:7: note:   no known conversion for argument 1 from ‘beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >’ to ‘std::__cxx11::basic_string<char>&&’  
/usr/include/c++/5/bits/basic_string.h:465:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type, _CharT, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type = long unsigned int]  
       basic_string(size_type __n, _CharT __c, const _Alloc& __a = _Alloc())  
       ^  
/usr/include/c++/5/bits/basic_string.h:465:7: note:   candidate expects 3 arguments, 1 provided  
/usr/include/c++/5/bits/basic_string.h:455:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _CharT*, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(const _CharT* __s, const _Alloc& __a = _Alloc())  
       ^  
/usr/include/c++/5/bits/basic_string.h:455:7: note:   no known conversion for argument 1 from ‘beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >’ to ‘const char*’  
/usr/include/c++/5/bits/basic_string.h:445:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _CharT*, std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type = long unsigned int]  
       basic_string(const _CharT* __s, size_type __n,  
       ^  
/usr/include/c++/5/bits/basic_string.h:445:7: note:   candidate expects 3 arguments, 1 provided  
/usr/include/c++/5/bits/basic_string.h:427:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type, std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type, const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type = long unsigned int]  
       basic_string(const basic_string& __str, size_type __pos,  
       ^  
/usr/include/c++/5/bits/basic_string.h:427:7: note:   candidate expects 4 arguments, 1 provided  
/usr/include/c++/5/bits/basic_string.h:411:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&, std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type, std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>; std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::size_type = long unsigned int]  
       basic_string(const basic_string& __str, size_type __pos,  
       ^  
/usr/include/c++/5/bits/basic_string.h:411:7: note:   candidate expects 3 arguments, 1 provided  
/usr/include/c++/5/bits/basic_string.h:399:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const std::__cxx11::basic_string<_CharT, _Traits, _Alloc>&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(const basic_string& __str)  
       ^  
/usr/include/c++/5/bits/basic_string.h:399:7: note:   no known conversion for argument 1 from ‘beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >’ to ‘const std::__cxx11::basic_string<char>&’  
/usr/include/c++/5/bits/basic_string.h:391:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string(const _Alloc&) [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string(const _Alloc& __a)  
       ^  
/usr/include/c++/5/bits/basic_string.h:391:7: note:   no known conversion for argument 1 from ‘beast::http::message<false, beast::http::string_body, beast::http::basic_headers<std::allocator<char> > >’ to ‘const std::allocator<char>&’  
/usr/include/c++/5/bits/basic_string.h:380:7: note: candidate: std::__cxx11::basic_string<_CharT, _Traits, _Alloc>::basic_string() [with _CharT = char; _Traits = std::char_traits<char>; _Alloc = std::allocator<char>]  
       basic_string()  
       ^  
/usr/include/c++/5/bits/basic_string.h:380:7: note:   candidate expects 0 arguments, 1 provided  
[  9%] Building CXX object CMakeFiles/tfs.dir/src/api_server/server.cpp.o  
[ 10%] Building CXX object CMakeFiles/tfs.dir/src/ban.cpp.o  
CMakeFiles/tfs.dir/build.make:119: recipe for target 'CMakeFiles/tfs.dir/src/api_server/peer.cpp.o' failed  
make[2]: *** [CMakeFiles/tfs.dir/src/api_server/peer.cpp.o] Error 1  
make[2]: *** Waiting for unfinished jobs....  
CMakeFiles/Makefile2:122: recipe for target 'CMakeFiles/tfs.dir/all' failed  
make[1]: *** [CMakeFiles/tfs.dir/all] Error 2  
Makefile:75: recipe for target 'all' failed  
make: *** [all] Error 2  
*** Failure: Exit code 2 ***  
```  
  
The issue can be worked around by constructing a functor "manually" and moving the response with the use of std::move(). Also reproduces with clang-3.6.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-20 03:21:14 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261755814  

Try:  
  
```  
    template<class U  
#if ! GENERATING_DOCS  
        , class = typename std::enable_if<  
            ! std::is_convertible<typename std::decay<U>::type, base_type>::value &&  
            ! std::is_convertible<typename std::decay<U>::type, message>::value>  
#endif  
    >  
    explicit  
    message(U&& u)  
        : body(std::forward<U>(u))  
    {  
    }  
```  
  
?

---

## Comment 2

> Username: djarek  
> Created at: 2016-11-20 03:29:31 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261756115  

@vinniefalco   
Still the same. I find it weird that the argument to enable_if is false and the compiler is still attempting to use the constructor:  
  
```  
<template-parameter-2-2> = std::enable_if<false, void>;   
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-11-20 13:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261777928  

I can reproduce your issue using gcc (but not msvc). Haven't tried clang. You're right, something looks very odd about this. I am investigating, thank you for the report.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-11-20 13:24:14 UTC  
> Updated at: 2016-11-20 13:25:22 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261778175  

Also fails with clang:  
https://travis-ci.org/vinniefalco/Beast/jobs/177418664  
  
Another data point, `message` is non-copyable - this code fails to compile:  
  
```  
response<string_body> res;  
response<string_body> res2(res);  
```

---

## Comment 5

> Username: HowardHinnant  
> Created at: 2016-11-20 15:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261783609  

Needs trailing `::type` on the `enable_if` on line 285.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-11-20 15:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261785083  

**derp**

---

## Comment 7

> Username: vinniefalco  
> Created at: 2016-11-20 15:40:35 UTC  
> Updated at: 2016-11-20 15:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-261785427  

Well that's embarassing. It should be fixed in in **1.0.0-b21**: available here: https://github.com/vinniefalco/Beast/tree/b21

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-01-10 23:50:34 UTC  
> Url: https://github.com/boostorg/beast/issues/196#issuecomment-271735401  

I'll close this for now. If its still a problem, feel free to re-open it or create a new issue - thanks!
