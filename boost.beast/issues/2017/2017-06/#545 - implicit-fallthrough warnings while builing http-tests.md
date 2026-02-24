# #545 - implicit-fallthrough warnings while builing http-tests [Closed]

> Username: octopus-prime  
> Created at: 2017-06-26 21:42:09 UTC  
> Updated at: 2017-06-27 22:04:42 UTC  
> Closed at: 2017-06-27 22:04:42 UTC  
> Url: https://github.com/boostorg/beast/issues/545  

Beast/v68  
gcc 7.0.1 -std=c++17  
boost 1.63  
  
So many warnings, you can't see real errors...  
  
```  
In file included from /home/mike/workspace/Beast/include/beast/http/serializer.hpp:333:0,  
                 from /home/mike/workspace/Beast/include/beast/http.hpp:24,  
                 from /home/mike/workspace/Beast/include/beast.hpp:14,  
                 from /home/mike/workspace/Beast/./example/doc/http_examples.hpp:8,  
                 from /home/mike/workspace/Beast/test/http/doc_examples.cpp:8:  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp: In member function ‘void beast::http::serializer<isRequest, Body, Fields, ChunkDecorator>::get(beast::error_code&, Visit&&) [with Visit = beast::http::detail::write_lambda<beast::test::pipe::stream>&; bool isRequest = false; Body = file_body; Fields = beast::http::basic_fields<std::allocator<char> >; ChunkDecorator = beast::http::no_chunk_decorator]’:  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:65:9: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         s_ = do_init;  
         ^~  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:69:5: note: here  
     case do_init:  
     ^~~~  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:89:26: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         BOOST_FALLTHROUGH;  
                          ^  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:92:5: note: here  
     case do_header:  
     ^~~~  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:98:9: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         s_ = do_header_only;  
         ^~  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:99:5: note: here  
     case do_header_only:  
     ^~~~  
/home/mike/workspace/Beast/include/beast/http/impl/serializer.ipp:110:9: warning: this statement may fall through [-Wimplicit-fallthrough=]  
         s_ = do_body + 1;  
         ^~  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-26 21:46:14 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311191422  

Is there a way to disable the warning? What do you suggest I do about this? I don't have gcc-7 in the Traivs matrix nor do I develop with it. I'm a good citizen and using `BOOST_FALLTHROUGH` in the right spots. So how do we fix it?

---

## Comment 2

> Username: octopus-prime  
> Created at: 2017-06-26 22:03:02 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311194903  

For me with boost 1.63 the macro is defined as:  
  
```  
#ifndef BOOST_FALLTHROUGH  
#  define BOOST_FALLTHROUGH ((void)0)  
#endif  
```  
  
That's not nice... For C++17 i would expect something like  
  
```  
#  define BOOST_FALLTHROUGH [[fallthrough]]  
```  
  
I tried this  
  
```  
        case do_deflate:  
            BOOST_ASSERT(! d.ws.wr_block_);  
            d.ws.wr_block_ = &d;  
            [[fallthrough]];  
//            BOOST_FALLTHROUGH;  
        case do_deflate + 1:  
```  
  
and the warning for `case do_deflate + 1` disappears.

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-06-26 22:07:44 UTC  
> Updated at: 2017-06-26 22:11:15 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311195875  

I am not sure, that boost will fix it in near future.  
  
Looked at  
https://github.com/boostorg/config/blob/develop/include/boost/config/detail/suffix.hpp  
  
It's the same macro.

---

## Comment 4

> Username: octopus-prime  
> Created at: 2017-06-26 22:10:09 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311196405  

So the only way i see is to define our own macro using  
`BOOST_FALLTHROUGH`  
as default and  
`[[fallthrough]]`  
for gcc -std=c++17.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-26 22:19:27 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311198242  

How do you detect C++17 using the preprocessor?

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-06-26 22:45:40 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311202885  

google says  
`  
__cplusplus >= 201500  
`  
could work.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-06-27 06:54:22 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311269676  

This does not work, sorry.  
Even this does not work too:  
```  
#if defined(__GNUC__) && (__GNUC__ >= 7)  
# define BEAST_FALLTHROUGH [[fallthrough]]  
#else  
# define BEAST_FALLTHROUGH BOOST_FALLTHROUGH  
#endif  
```  
and this does not work too:  
```  
# define BEAST_FALLTHROUGH [[fallthrough]]  
```

---

## Comment 8

> Username: octopus-prime  
> Created at: 2017-06-27 06:55:18 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311269842  

So the next option is to disable the warning.  
I think there are pragmas to do that...

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-06-27 07:21:12 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311274844  

And i think i was incorrect about my builds.  
I use gcc 7 which has c++17 support.  
But the makefiles generated by cmake will not use it, right?  
  
Nevertheless. I will investigate later...

---

## Comment 10

> Username: octopus-prime  
> Created at: 2017-06-27 11:26:03 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311330229  

Nice article:  
https://dzone.com/articles/implicit-fallthrough-in-gcc-7

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-06-27 11:48:56 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311334610  

I don't see any `#pragma` there. I can disable the warning in the CMakeLists.txt and Jamfile, but someone who is using Beast in their own project is still going to get the warning unless they also disable it.

---

## Comment 12

> Username: octopus-prime  
> Created at: 2017-06-27 16:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311406303  

No. The article was about fallthrough in general.  
To disable warnings via pragmas in code:  
  
- https://gcc.gnu.org/onlinedocs/gcc/Diagnostic-Pragmas.html  
- https://stackoverflow.com/questions/3378560/how-to-disable-gcc-warnings-for-a-few-lines-of-code  
  
That are the pragmas i was talking about...  
As i said, i will investigate after work.  
Do you have a gcc-7 for testing?!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-06-27 16:14:26 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311408243  

>Do you have a gcc-7 for testing?!  
  
Nope.

---

## Comment 14

> Username: octopus-prime  
> Created at: 2017-06-27 17:54:39 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311435715  

The pragmas seems to work:  
  
At beginning of problematic file:  
```  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wimplicit-fallthrough"  
```  
and at end:  
`  
#pragma GCC diagnostic pop  
`  
  
Example:  
```  
#ifndef BEAST_HTTP_IMPL_SERIALIZER_IPP  
#define BEAST_HTTP_IMPL_SERIALIZER_IPP  
  
#include <beast/http/error.hpp>  
#include <beast/http/status.hpp>  
#include <beast/core/detail/config.hpp>  
#include <boost/assert.hpp>  
#include <ostream>  
  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wimplicit-fallthrough"  
  
namespace beast {  
namespace http {  
  
template<bool isRequest, class Body,  
    class Fields, class ChunkDecorator>  
void  
serializer<isRequest, Body, Fields, ChunkDecorator>::  
frdinit(std::true_type)  
{  
    frd_.emplace(m_, m_.version, m_.method());  
}  
```  
...  
```  
    default:  
        BOOST_ASSERT(false);  
    case do_complete:  
        break;  
  
    go_complete:  
        s_ = do_complete;  
        break;  
    }  
}  
  
} // http  
} // beast  
  
#pragma GCC diagnostic pop  
  
#endif  
```  
  
How does clang and msvc handle these pragmas?!  
What about gcc not knowing this warning to ignore?  
  
So may be a hard switch for gcc >= 7 in cmake/bjam  
`-Wimplicit-fallthrough=0`  
is the better option?!

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-06-27 17:56:49 UTC  
> Updated at: 2017-06-27 17:57:33 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311436299  

Try this:  
```  
#include <boost/config.hpp>  
  
#if BOOST_WORKAROUND(BOOST_GCC, >= 70000)  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wimplicit-fallthrough"  
#endif  
  
...  
  
#if BOOST_WORKAROUND(BOOST_GCC, >= 70000)  
#pragma GCC diagnostic pop  
#endif  
```  
  
I can get this into **v69** if it works.

---

## Comment 16

> Username: octopus-prime  
> Created at: 2017-06-27 18:55:02 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311451927  

For C++ files it worked. Be aware! I had to change about 20 files! Mostly ipp files...  
  
But there is a file  
`Beast/test/benchmarks/nodejs-parser/http_parser.c`  
which does not like the macros.

---

## Comment 17

> Username: octopus-prime  
> Created at: 2017-06-27 18:57:08 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311452517  

```  
mike@workstation:~/workspace/Beast/bin$ git status  
Auf Branch v69  
Änderungen, die nicht zum Commit vorgemerkt sind:  
  (benutzen Sie "git add <Datei>..." um die Änderungen zum Commit vorzumerken)  
  (benutzen Sie "git checkout -- <Datei>..." um die Änderungen im Arbeitsverzeichnis zu verwerfen)  
  
	geändert:       ../example/common/detect_ssl.hpp  
	geändert:       ../include/beast/core/impl/buffered_read_stream.ipp  
	geändert:       ../include/beast/http/impl/basic_parser.ipp  
	geändert:       ../include/beast/http/impl/read.ipp  
	geändert:       ../include/beast/http/impl/serializer.ipp  
	geändert:       ../include/beast/http/impl/write.ipp  
	geändert:       ../include/beast/http/serializer.hpp  
	geändert:       ../include/beast/websocket/detail/mask.hpp  
	geändert:       ../include/beast/websocket/impl/close.ipp  
	geändert:       ../include/beast/websocket/impl/ping.ipp  
	geändert:       ../include/beast/websocket/impl/read.ipp  
	geändert:       ../include/beast/websocket/impl/write.ipp  
	geändert:       ../include/beast/zlib/detail/inflate_stream.hpp  
	geändert:       ../test/websocket/websocket_async_echo_server.hpp  
  
Unbeobachtete Dateien:  
  (benutzen Sie "git add <Datei>..." um die Änderungen zum Commit vorzumerken)  
  
	../.project  
  
keine Änderungen zum Commit vorgemerkt (benutzen Sie "git add" und/oder "git commit -a")  
mike@workstation:~/workspace/Beast/bin$   
```

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-06-27 18:57:36 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311452647  

>Beast/test/benchmarks/nodejs-parser/http_parser.c  
  
That's compiled with C. You can set the compiler switch on that one, I'm not sure how to do it on just one target in both cmake and bjam:  
https://github.com/vinniefalco/Beast/blob/master/test/benchmarks/CMakeLists.txt  
https://github.com/vinniefalco/Beast/blob/master/test/benchmarks/Jamfile

---

## Comment 19

> Username: vinniefalco  
> Created at: 2017-06-27 18:57:54 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311452729  

Do you want to submit a pull request?

---

## Comment 20

> Username: octopus-prime  
> Created at: 2017-06-27 19:12:44 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311456483  

I replaced the usage of  
`BOOST_WORKAROUND`  
macro by using the guard  
`#if defined(__GNUC__) && (__GNUC__ >= 7)`  
  
Example:  
```  
#ifdef _MSC_VER  
# pragma warning (push)  
# pragma warning (disable: 4127) // conditional expression is constant  
# pragma warning (disable: 4244) // integer conversion, possible loss of data  
#endif  
  
#if defined(__GNUC__) && (__GNUC__ >= 7)  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wimplicit-fallthrough"  
#endif  
  
#include "nodejs-parser/http_parser.c"  
  
#ifdef _MSC_VER  
# pragma warning (pop)  
#endif  
  
#if defined(__GNUC__) && (__GNUC__ >= 7)  
#pragma GCC diagnostic pop  
#endif  
```  
  
As you can see:  
  
- no `#include <boost/config.hpp>` needed  
- works for c stuff too  
  
So... no warnings at all :-)

---

## Comment 21

> Username: octopus-prime  
> Created at: 2017-06-27 19:14:34 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311456933  

Modified files:  
  
```  
	geändert:       ../example/common/detect_ssl.hpp  
	geändert:       ../include/beast/core/impl/buffered_read_stream.ipp  
	geändert:       ../include/beast/http/impl/basic_parser.ipp  
	geändert:       ../include/beast/http/impl/read.ipp  
	geändert:       ../include/beast/http/impl/serializer.ipp  
	geändert:       ../include/beast/http/impl/write.ipp  
	geändert:       ../include/beast/http/serializer.hpp  
	geändert:       ../include/beast/websocket/detail/mask.hpp  
	geändert:       ../include/beast/websocket/impl/close.ipp  
	geändert:       ../include/beast/websocket/impl/ping.ipp  
	geändert:       ../include/beast/websocket/impl/read.ipp  
	geändert:       ../include/beast/websocket/impl/write.ipp  
	geändert:       ../include/beast/zlib/detail/inflate_stream.hpp  
	geändert:       ../test/benchmarks/nodejs_parser.cpp  
	geändert:       ../test/websocket/websocket_async_echo_server.hpp  
```

---

## Comment 22

> Username: vinniefalco  
> Created at: 2017-06-27 19:14:44 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311456977  

Boost libraries *must* use `<boost/config.hpp>` there are compilers which pretend to be GCC so `__GNUC__` is unreliable.

---

## Comment 23

> Username: octopus-prime  
> Created at: 2017-06-27 19:15:03 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311457041  

Ouch!!

---

## Comment 24

> Username: vinniefalco  
> Created at: 2017-06-27 19:20:44 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311458380  

Actually upon re-reading your message, it looks like you are suggesting to use `__GNUC__` only for nodejs_parser.cpp? Thats okay. We just have to use boost/config.hpp in header files in `beast/include`. If you submit a pull request, I'll merge it.

---

## Comment 25

> Username: octopus-prime  
> Created at: 2017-06-27 19:31:38 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311461130  

Added pull request  
  
`include/beast/http/serializer.hpp` has conficts...  
  
I think it's your change  
```  
# if BOOST_WORKAROUND(BOOST_GCC, < 50000) || BOOST_VERSION < 106400  
```  
==>  
```  
# if BOOST_WORKAROUND(BOOST_GCC, < 50000) && BOOST_VERSION < 106400  
```  
i missed

---

## Comment 26

> Username: octopus-prime  
> Created at: 2017-06-27 19:35:48 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311462167  

Resolved conflict using the "&&" variant.  
Now: "This branch cannot be rebased due to conflicts"  
It drives me crazy :D

---

## Comment 27

> Username: vinniefalco  
> Created at: 2017-06-27 19:44:44 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311464405  

Stop then. I have another approach

---

## Comment 28

> Username: vinniefalco  
> Created at: 2017-06-27 19:46:44 UTC  
> Updated at: 2017-06-27 19:47:11 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311464893  

I rewrote the branch, I think this will solve the warnings once and for all:  
https://github.com/vinniefalco/Beast/commits/v69  
  
See:  
https://github.com/vinniefalco/Beast/commit/438bbd21a76b1de417dbbce34d6d459a3bce6999#diff-babe0214b8c9849560c0eddf6e8ebb10R14  
  
Although I might need to copy your fix for nodejs_parser.cpp

---

## Comment 29

> Username: octopus-prime  
> Created at: 2017-06-27 20:16:56 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311472576  

Nope.  
  
```  
#if BOOST_VERSION >= 106500 || ! defined(BOOST_GCC) || BOOST_GCC < 70000  
# define BEAST_FALLTHROUGH BOOST_FALLTHROUGH  
#else  
# define BEAST_FALLTHROUGH _attribute__((fallthrough))  
#endif  
```  
should be  
```  
#if BOOST_VERSION >= 106500 || ! defined(BOOST_GCC) || BOOST_GCC < 70000  
# define BEAST_FALLTHROUGH BOOST_FALLTHROUGH  
#else  
# define BEAST_FALLTHROUGH __attribute__((fallthrough))  
#endif  
```  
Note `__attribute__` instead of `_attribute__`  
I fixed it local. **But all warnings are up agian** :-(

---

## Comment 30

> Username: vinniefalco  
> Created at: 2017-06-27 20:25:39 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311474776  

LOL... g-d...

---

## Comment 31

> Username: vinniefalco  
> Created at: 2017-06-27 20:27:36 UTC  
> Updated at: 2017-06-27 20:29:38 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311475274  

You said you're on "gcc 7.0.1"?  
Can you repro this defect here?  
https://wandbox.org/  
  
It works in 7.1.0:  
https://wandbox.org/permlink/jiO5JTGtqZz2vSUy  
  
Isn't **gcc 7.0.1** a pre-release version? Beast can't support non-release versions of the toolchain!

---

## Comment 32

> Username: octopus-prime  
> Created at: 2017-06-27 20:36:55 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311477768  

```  
#include <iostream>  
#include <cstdlib>  
  
#define BEAST_FALLTHROUGH [[fallthrough]]  
  
void foo(int i)  
{  
    int n = 0;  
    switch (i)  
    {  
        case 0:  
            {  
            ++n;  
            BEAST_FALLTHROUGH;  
            }  
        case 1:  
            ++n;  
            break;  
    }  
}  
  
int main()  
{  
    std::cout << "Hello, Wandbox!" << std::endl;  
    foo(rand());  
}  
```  
  
Works with gcc 7.1.0 and 8.0.0  
  
And `#define BEAST_FALLTHROUGH __attribute__((fallthrough))` works too.

---

## Comment 33

> Username: vinniefalco  
> Created at: 2017-06-27 20:37:47 UTC  
> Updated at: 2017-06-27 20:37:54 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311477990  

Okay so what action is required? I'm very confused now

---

## Comment 34

> Username: octopus-prime  
> Created at: 2017-06-27 20:38:09 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311478081  

I would like to update gcc!!

---

## Comment 35

> Username: octopus-prime  
> Created at: 2017-06-27 20:39:05 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311478358  

But 7.0.1 is the highest version in repository for Ubuntu 17.04 :-(

---

## Comment 36

> Username: pdimov  
> Created at: 2017-06-27 20:46:26 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311480296  

If you use bjam, you can add `using gcc : : : <compileflags>-Wno-implicit-fallthrough ;` to `~/user-config.jam`.

---

## Comment 37

> Username: vinniefalco  
> Created at: 2017-06-27 21:33:58 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311492792  

Is this resolved by **v69** then?

---

## Comment 38

> Username: octopus-prime  
> Created at: 2017-06-27 21:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311494147  

I am totally confused now. Tried to get bjam running.  
So this works well with bjam:  
  
- `using gcc : 7 : : <cxxflags>-march=native <cxxflags>-ftemplate-depth=256 <cxxflags>-std=c++17 ;`  
- `using gcc : 6 : : <cxxflags>-march=native <cxxflags>-ftemplate-depth=256 <cxxflags>-std=c++14 ;`  
- `using clang : 4 : : <cxxflags>-march=native <cxxflags>-std=c++1z ;`  
  
WTH was building with cmake generated Makefile?! Don't know...

---

## Comment 39

> Username: octopus-prime  
> Created at: 2017-06-27 21:41:52 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311494619  

Got 2 warnings with gcc 7:  
  
```  
test/core/static_string.cpp: In member function ‘void beast::static_string_test::testGeneral()’:  
test/core/static_string.cpp:1394:32: warning: ‘*((void*)& s4 +8)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             BEAST_EXPECT(s4[0] == 'x');  
                                ^~  
test/core/static_string.cpp:1395:32: warning: ‘*((void*)& s4 +9)’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
             BEAST_EXPECT(s4[1] == 'y');  
                                ^~  
```  
  
And this one with clang 4:  
```  
test/zlib/zlib-1.2.8/inflate.c:1507:61: warning: shifting a negative signed value is undefined [-Wshift-negative-value]  
    if (strm == Z_NULL || strm->state == Z_NULL) return -1L << 16;  
                                                        ~~~ ^  
```

---

## Comment 40

> Username: octopus-prime  
> Created at: 2017-06-27 21:42:47 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311494819  

But implicit-fallthrough hell has gone... So i suggest closing this issue.

---

## Comment 41

> Username: vinniefalco  
> Created at: 2017-06-27 21:48:33 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311496072  

> `test/zlib/zlib-1.2.8/inflate.c:1507:61:`  
  
That's normal zlib. The `static_string`, no idea.

---

## Comment 42

> Username: vinniefalco  
> Created at: 2017-06-27 21:49:05 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311496191  

You should not need `<cxxflags>-ftemplate-depth=256`, is that still needed?

---

## Comment 43

> Username: octopus-prime  
> Created at: 2017-06-27 21:50:22 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311496466  

Is default in my site-config.jam  
For other heavy stuff (e.g. boost spirit)

---

## Comment 44

> Username: octopus-prime  
> Created at: 2017-06-27 21:55:50 UTC  
> Updated at: 2017-06-27 21:56:37 UTC  
> Url: https://github.com/boostorg/beast/issues/545#issuecomment-311497679  

`using gcc : : : <cxxflags>-march=native <cxxflags>-std=c++17 ;`  
works fine too ;-)  
So it's gcc 7.0.1
