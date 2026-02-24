# #376 - Reducing/controlling dynamic memory allocations - async_read [Closed]

> Username: WojciechMigda  
> Created at: 2017-05-23 13:05:28 UTC  
> Updated at: 2017-05-24 21:35:14 UTC  
> Closed at: 2017-05-24 21:35:14 UTC  
> Url: https://github.com/boostorg/beast/issues/376  

I am repeatedly calling `async_read` to pull order book feed from an exchange in a fashion like this:  
```  
 void on_read(const boost::system::error_code& ec)  
    {  
        m_b.consume(m_b.size());  
  
        m_ws.async_read(m_op, m_b,  
            [&](const boost::system::error_code& ec)  
            {  
                on_read(ec);  
            });  
    }  
```  
  
with relevant class members:  
```  
    beast::websocket::opcode m_op;  
    beast::multi_buffer m_b;  
```  
  
valgrind indicates that with each async_read cycle there is about a dozen of dynamic memory allocations being made. Is there a possibility to reduce that number? Replacing multi_buffer with something else? Providing custom pool allocators?

---

## Comment 1

> Username: WojciechMigda  
> Created at: 2017-05-23 13:07:25 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303391795  

Thank you for an awesome library!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-23 14:36:13 UTC  
> Updated at: 2017-05-23 14:43:19 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303418217  

First I need to know if you have permessage-deflate turned on. I believe its on by default in client mode and off by default in server mode but you would need to check. Having permessage-deflate active on a connection will increase the number of allocations required (since it introduces input and output latency).  
  
Otherwise, you have several choices for controlling the number of memory allocations:  
  
* Construct the `multi_buffer` with a larger value for `alloc_size` (see http://vinniefalco.github.io/beast/beast/ref/basic_multi_buffer/basic_multi_buffer/overload7.html)  
  
* Use `flat_buffer` instead of `multi_buffer` (http://vinniefalco.github.io/beast/beast/ref/flat_buffer.html). Re-using the object can eliminate allocations (consume the data before using it again on a new message).  
  
* Use `static_buffer_n` instead of `multi_buffer` if you know that messages will never exceed a certain size (http://vinniefalco.github.io/beast/beast/ref/static_buffer_n.html)  
  
* Call `async_read_frame` in a loop instead of calling `async_read` (I might be able to eliminate a couple of allocations caused by `async_read`, I will open an issue)  
  
* Provide overloads of the functions `asio_handler_allocate` and `asio_handler_deallocate` for your completion handler type to implement a custom memory management solution. You could use Boost.Pool to help. This is an advanced topic, you can read about it here: http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/overview/core/allocation.html and  http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/example/cpp11/allocation/server.cpp  
  
Please let me know how well each of these solutions works for you.

---

## Comment 3

> Username: WojciechMigda  
> Created at: 2017-05-23 15:07:48 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303428666  

When I try `static_buffer_n` (`beast::static_buffer_n<4096> m_b;`) compilation fails at the line where I call `async_read`:  
```  
/repo/wss/src/client_async.cpp:116:14:   required from here  
/repo/wss/Beast/include/beast/websocket/detail/pmd_extension.hpp:372:29: error: no matching function for call to ‘read_size_helper(beast::static_buffer_n<4096ul>&, int)’  
             read_size_helper(buffer, 65536));  
```  
With `flat_buffer` it fails with another reason:  
```  
repo/wss/src/client_async.cpp:116:14:   required from here  
/repo/wss/Beast/include/beast/websocket/detail/utf8_checker.hpp:131:5: error: static assertion failed: ConstBufferSequence requirements not met  
     static_assert(is_const_buffer_sequence<ConstBufferSequence>::value,  
     ^  
/repo/wss/Beast/include/beast/websocket/detail/utf8_checker.hpp:135:5: error: ‘begin’ was not declared in this scope  
     for(auto const& b : bs)  
     ^  
```  
Maybe I am doing something wrong?  
  
Increasing `multi_buffer` size didn't help much - there seems to be more variation in number of allocation between the runs, though.  
  
As for `pmd` I am not doing anything explicit about it when I put all the wires together. I will try this when I figure out how one disables it.  
Thank you.

---

## Comment 4

> Username: WojciechMigda  
> Created at: 2017-05-23 15:09:05 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303429095  

One detail - I am connecting to a wss stream and I am using g++ / -std=c++1z.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-05-23 15:34:42 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303437446  

Phew, looks like you found some actual bugs in the code! I'll put together a patch right away.

---

## Comment 6

> Username: WojciechMigda  
> Created at: 2017-05-23 15:34:50 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303437476  

Re `pmd`: I put this before first write/reads on the wss stream  
```  
        beast::websocket::permessage_deflate pmd;  
        pmd.client_enable = false;  
        pmd.server_enable = false;  
        m_ws.set_option(pmd);  
```  
and unfortunately it doesn't show any significant improvement.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-05-23 16:34:25 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303459444  

That narrows it down

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-05-23 16:59:27 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303466218  

>With flat_buffer it fails with another reason:  
  
I'm having a hard time reproducing the compilation error you see with `flat_buffer`. Do you have more of the error output? Can you put together a small test program that reproduces the issue?  
  
Thanks

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-05-23 17:00:36 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303466575  

This branch fixes the `read_size_helper` errors:  
https://github.com/vinniefalco/Beast/commits/buffers

---

## Comment 10

> Username: WojciechMigda  
> Created at: 2017-05-23 17:38:53 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303476811  

> I'm having a hard time reproducing the compilation error you see with flat_buffer. Do you have more of the error output? Can you put together a small test program that reproduces the issue?  
  
I've shared my bitbucket repo with you. The branch is `async_read_flat_buffer`. Just clone w/ submodule, then `cmake -DCMAKE_CXX_COMPILER=c++.sh` and `make`. The file to look at is `src/client_async.cpp`.  
Thank you.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-05-23 17:49:26 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303479746  

I don't know what "c++.sh" means (I'm on Windows, using Microsoft Visual Studio). I cloned your repository and I generated a set of Visual Studio project files and the solution using CMake. I opened it up, added an include directory for OpenSSL and I was able to successfully build your client_async.cpp. I got these warnings:  
  
```  
1>------ Build started: Project: client_async, Configuration: Debug Win32 ------  
1>client_async.cpp  
1>Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately. For example:  
1>- add -D_WIN32_WINNT=0x0501 to the compiler command line; or  
1>- add _WIN32_WINNT=0x0501 to your project's Preprocessor Definitions.  
1>Assuming _WIN32_WINNT=0x0501 (i.e. Windows XP target).  
1>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.10.25017\include\xutility(2889): warning C4996: 'std::equal::_Unchecked_iterators::_Deprecate': Call to 'std::equal' with parameters that may be unsafe - this call relies on the caller to check that the passed values are correct. To disable this warning, use -D_SCL_SECURE_NO_WARNINGS. See documentation on how to use Visual C++ 'Checked Iterators'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.10.25017\include\xutility(2889): note: see declaration of 'std::equal::_Unchecked_iterators::_Deprecate'  
1>C:\Users\vinnie\lib\boost_1_64_0\boost/range/algorithm/equal.hpp(128): note: see reference to function template instantiation 'bool std::equal<RandomAccessTraversalReadableIterator1,RandomAccessTraversalReadableIterator2,BinaryPredicate>(_InIt1,_InIt1,_InIt2,_Pr)' being compiled  
1>        with  
1>        [  
1>            RandomAccessTraversalReadableIterator1=const char *,  
1>            RandomAccessTraversalReadableIterator2=const char *,  
1>            BinaryPredicate=beast::detail::ci_equal_pred,  
1>            _InIt1=const char *,  
1>            _InIt2=const char *,  
1>            _Pr=beast::detail::ci_equal_pred  
1>        ]  
1>C:\Users\vinnie\lib\boost_1_64_0\boost/range/algorithm/equal.hpp(156): note: see reference to function template instantiation 'bool boost::range_detail::equal_impl<SinglePassTraversalReadableIterator1,SinglePassTraversalReadableIterator2,BinaryPredicate>(RandomAccessTraversalReadableIterator1,RandomAccessTraversalReadableIterator1,RandomAccessTraversalReadableIterator2,RandomAccessTraversalReadableIterator2,BinaryPredicate,std::random_access_iterator_tag,std::random_access_iterator_tag)' being compiled  
1>        with  
1>        [  
1>            SinglePassTraversalReadableIterator1=const char *,  
1>            SinglePassTraversalReadableIterator2=const char *,  
1>            BinaryPredicate=beast::detail::ci_equal_pred,  
1>            RandomAccessTraversalReadableIterator1=const char *,  
1>            RandomAccessTraversalReadableIterator2=const char *  
1>        ]  
1>C:\Users\vinnie\lib\boost_1_64_0\boost/range/algorithm/equal.hpp(193): note: see reference to function template instantiation 'bool boost::range_detail::equal<const char*,const char*,BinaryPredicate>(SinglePassTraversalReadableIterator1,SinglePassTraversalReadableIterator1,SinglePassTraversalReadableIterator2,SinglePassTraversalReadableIterator2,BinaryPredicate)' being compiled  
1>        with  
1>        [  
1>            BinaryPredicate=beast::detail::ci_equal_pred,  
1>            SinglePassTraversalReadableIterator1=const char *,  
1>            SinglePassTraversalReadableIterator2=const char *  
1>        ]  
1>C:\Users\vinnie\src\gds\Beast\include\beast/core/detail/ci_char_traits.hpp(99): note: see reference to function template instantiation 'bool boost::range::equal<T,beast::string_view,beast::detail::ci_equal_pred>(const SinglePassRange1 &,const SinglePassRange2 &,BinaryPredicate)' being compiled  
1>        with  
1>        [  
1>            T=beast::string_view,  
1>            SinglePassRange1=beast::string_view,  
1>            SinglePassRange2=beast::string_view,  
1>            BinaryPredicate=beast::detail::ci_equal_pred  
1>        ]  
1>C:\Users\vinnie\src\gds\Beast\include\beast/websocket/detail/pmd_extension.hpp(84): note: see reference to function template instantiation 'bool beast::detail::ci_equal<_Ty1,char[19]>(const S1 &,const S2 (&))' being compiled  
1>        with  
1>        [  
1>            _Ty1=beast::string_view,  
1>            S1=beast::string_view,  
1>            S2=char [19]  
1>        ]  
1>C:\Users\vinnie\src\gds\Beast\include\beast/websocket/impl/handshake.ipp(133): note: see reference to function template instantiation 'void beast::websocket::detail::pmd_read<beast::http::basic_fields<std::allocator<char>>>(beast::websocket::detail::pmd_offer &,const Fields &)' being compiled  
1>        with  
1>        [  
1>            Fields=beast::http::basic_fields<std::allocator<char>>  
1>        ]  
1>C:\Users\vinnie\src\gds\Beast\include\beast/websocket/impl/handshake.ipp(118): note: while compiling class template member function 'void beast::websocket::stream<ws::async_echo_client::stream_type &>::handshake_op<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>::operator ()(beast::error_code,bool)'  
1>C:\Users\vinnie\src\gds\Beast\include\beast/websocket/impl/handshake.ipp(73): note: see reference to function template instantiation 'void beast::websocket::stream<ws::async_echo_client::stream_type &>::handshake_op<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>::operator ()(beast::error_code,bool)' being compiled  
1>C:\Users\vinnie\src\gds\Beast\include\beast/core/impl/handler_ptr.ipp(48): note: while compiling class template member function 'beast::handler_ptr<beast::websocket::stream<ws::async_echo_client::stream_type &>::handshake_op<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>::data,Handler>::~handler_ptr(void)'  
1>        with  
1>        [  
1>            Handler=ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>  
1>        ]  
1>C:\Users\vinnie\src\gds\src\client_async.cpp(279): note: see reference to function template instantiation 'beast::handler_ptr<beast::websocket::stream<ws::async_echo_client::stream_type &>::handshake_op<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>::data,Handler>::~handler_ptr(void)' being compiled  
1>        with  
1>        [  
1>            Handler=ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>  
1>        ]  
1>C:\Users\vinnie\src\gds\Beast\include\beast/websocket/impl/handshake.ipp(61): note: see reference to class template instantiation 'beast::handler_ptr<beast::websocket::stream<ws::async_echo_client::stream_type &>::handshake_op<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>::data,Handler>' being compiled  
1>        with  
1>        [  
1>            Handler=ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>  
1>        ]  
1>C:\Users\vinnie\src\gds\Beast\include\beast/websocket/impl/handshake.ipp(182): note: see reference to class template instantiation 'beast::websocket::stream<ws::async_echo_client::stream_type &>::handshake_op<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>' being compiled  
1>C:\Users\vinnie\src\gds\src\client_async.cpp(150): note: see reference to function template instantiation 'void beast::websocket::stream<ws::async_echo_client::stream_type &>::async_handshake<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>(const beast::string_view &,const beast::string_view &,HandshakeHandler &&)' being compiled  
1>        with  
1>        [  
1>            HandshakeHandler=ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>  
1>        ]  
1>C:\Users\vinnie\src\gds\src\client_async.cpp(146): note: see reference to function template instantiation 'void beast::websocket::stream<ws::async_echo_client::stream_type &>::async_handshake<ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>>(const beast::string_view &,const beast::string_view &,HandshakeHandler &&)' being compiled  
1>        with  
1>        [  
1>            HandshakeHandler=ws::async_echo_client::handshake_handler::<lambda_1a838a016b161b5392ec46e007401609>  
1>        ]  
1>Done building project "client_async.vcxproj".  
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========  
```  
  
These warnings just mean that you haven't tuned your CMakeLists.txt for Windows. For example by setting the windows API version. These three lines from the Beast build scripts handle that:  
https://github.com/vinniefalco/Beast/blob/master/CMakeLists.txt#L11  
  
If you could try building with clang, the diagnostics are usually better. Or maybe there is more error output from gcc that you can include? I'd like to know the type of *ConstBufferSequence* where the error is generated.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-05-23 17:51:58 UTC  
> Updated at: 2017-05-23 17:52:43 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303480431  

Looking over your program, if you're doing what I think you're doing (a simple, one-connection client) you probably want to use coroutines they are much easier to program with. You don't have to mess with completion handlers. Your read/write loop would just be a single function, not many lines.  
  
Or you could maybe write the whole thing using the synchronous interfaces...

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-05-23 17:57:48 UTC  
> Updated at: 2017-05-23 17:57:55 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303482020  

Aha! I believe I have found your problem with `flat_buffer`. You just need to update to Beast v43 instead of Beast v42. The compilation error was fixed in this commit: https://github.com/vinniefalco/Beast/commit/3aa87e076ca19a897306f2bf3c782373a8f218be

---

## Comment 14

> Username: WojciechMigda  
> Created at: 2017-05-23 17:59:47 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303482607  

1. at the time of asking my question I was using 76f1084ecb010596d080ea96b9e47d15da649152 Beast's commit, with which `multi_buffer` version compiled fine. Now I switched to the latest master (dab679cb0a3cf51a5ecf4e52a832edcd88ff5609) and I get this:  
```  
[ 25%] Building CXX object CMakeFiles/client_async.dir/src/client_async.cpp.o  
In file included from /repo/github/gdax-scrapbook/Beast/include/beast/websocket/detail/frame.hpp:14:0,  
                 from /repo/github/gdax-scrapbook/Beast/include/beast/websocket/detail/stream_base.hpp:14,  
                 from /repo/github/gdax-scrapbook/Beast/include/beast/websocket/stream.hpp:14,  
                 from /repo/github/gdax-scrapbook/src/client_async.cpp:2:  
/repo/github/gdax-scrapbook/Beast/include/beast/core/consuming_buffers.hpp:110:45: error: expected ‘)’ before ‘,’ token  
     consuming_buffers(boost::in_place_init_t, Args&&... args);  
                                             ^  
In file included from /repo/github/gdax-scrapbook/Beast/include/beast/core/consuming_buffers.hpp:132:0,  
                 from /repo/github/gdax-scrapbook/Beast/include/beast/websocket/detail/frame.hpp:14,  
                 from /repo/github/gdax-scrapbook/Beast/include/beast/websocket/detail/stream_base.hpp:14,  
                 from /repo/github/gdax-scrapbook/Beast/include/beast/websocket/stream.hpp:14,  
                 from /repo/github/gdax-scrapbook/src/client_async.cpp:2:  
/repo/github/gdax-scrapbook/Beast/include/beast/core/impl/consuming_buffers.ipp:181:18: error: expected constructor, destructor, or type conversion before ‘(’ token  
 consuming_buffers(boost::in_place_init_t, Args&&... args)  
                  ^  
CMakeFiles/client_async.dir/build.make:62: recipe for target 'CMakeFiles/client_async.dir/src/client_async.cpp.o' failed  
make[2]: *** [CMakeFiles/client_async.dir/src/client_async.cpp.o] Error 1  
CMakeFiles/Makefile2:67: recipe for target 'CMakeFiles/client_async.dir/all' failed  
make[1]: *** [CMakeFiles/client_async.dir/all] Error 2  
Makefile:83: recipe for target 'all' failed  
make: *** [all] Error 2  
```  
When I checkout your `buffers` branch it doesn't compile neither.  
Have you changed any APIs in between?  
  
2. I do not have windows dev environment, sorry about my CMakeLists.txt.  
  
3. coroutines - sure, once I get familiar and comfortable with boost.asio and setup reliable metrics :) At this point I am trying to get a feel of the new framework.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-05-23 18:00:30 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303482828  

You need Boost 1.64.0 to use v43, see https://github.com/vinniefalco/Beast/commit/6b54d3ad0f2c936199e19f83161b893ceefccfb1

---

## Comment 16

> Username: WojciechMigda  
> Created at: 2017-05-23 18:02:00 UTC  
> Updated at: 2017-05-23 18:04:05 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303483253  

~~Ugh, my boost is 1.58, Beast requires 1.64 :( ~~  
You beat me to it.

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-05-23 18:04:57 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303484100  

Do you want me to add a workaround so that older versions of boost work?

---

## Comment 18

> Username: WojciechMigda  
> Created at: 2017-05-23 18:08:32 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303485054  

I am going to look at my `boost` options. Thank you very much for your help.

---

## Comment 19

> Username: WojciechMigda  
> Created at: 2017-05-23 18:10:43 UTC  
> Updated at: 2017-05-23 18:10:54 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303485616  

> Do you want me to add a workaround so that older versions of boost work?  
  
That would be great but I don't want to put too much pressure. I'll try to upgrade first.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2017-05-23 18:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303486434  

I will add a workaround, I have an idea for an easy hack

---

## Comment 21

> Username: vinniefalco  
> Created at: 2017-05-23 18:27:17 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303490047  

@WojciechMigda Okay, I rewrote history on the **buffers** branch. I think it will work with older Boosts now. I am building it on Travis.

---

## Comment 22

> Username: WojciechMigda  
> Created at: 2017-05-23 18:41:57 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303494066  

Amazing, my code compiles now with all three buffer types. I'll get back at the allocations now.  
Thank you again, @vinniefalco.

---

## Comment 23

> Username: WojciechMigda  
> Created at: 2017-05-23 19:49:39 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303512086  

I have collected stats regarding # allocations vs. buffer type and it appears that there is no dependency. For each setup (buffer, 100 / 200 iterations) I made 4 runs and picked the lowest number of allocations reported. In each case the difference in # of allocations between 100/200 versions across the three types of buffers was roughly the same, amounting to about 11 allocations per `async_read` iteration.

---

## Comment 24

> Username: WojciechMigda  
> Created at: 2017-05-23 20:09:37 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303517271  

When you meant coroutines did you mean fibers like, for instance, here?  
https://www.reddit.com/r/cpp/comments/5s8s2f/is_there_efficient_alternative_to_asio/ddgjorw/

---

## Comment 25

> Username: vinniefalco  
> Created at: 2017-05-23 20:39:41 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303525191  

>When you meant coroutines did you mean fibers like, for instance, here?  
  
I mean coroutines:  
http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/overview/core/spawn.html  
  
They probably won't help with your memory allocation problem but they can make the code easier to write.  
  
I have to ask, are the memory allocations an issue? Are you reaching a performance limit?

---

## Comment 26

> Username: WojciechMigda  
> Created at: 2017-05-23 21:13:23 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303534006  

Actually, in this case I was mostly driven by curiosiy. I did a lot of professional embedded development in the past where dynamic allocations were rare and pooled. Even away from daily work I am trying to make sure that the code I write does not do excessive dynamic allocations. Here, I did not expect seemingly simple statements to do as many allocations. The number seemed over the top and as a first time user of boost.asio and your library I wanted to find out whether I am not following patterns which maybe are functionally correct, but are not optimal from the perspective of optimal use of resources.

---

## Comment 27

> Username: vinniefalco  
> Created at: 2017-05-23 21:22:40 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303536277  

Fair enough. If you tried this one I'd like to know if it helped, I can make a change to make it automatic:  
  
* Call async_read_frame in a loop instead of calling async_read  
  
When move-only handlers come to Boost.Asio it will eliminate one additional allocation for each asynchronous operation. Its also possible to implement your own custom allocation strategy for completion handlers (last item on my list above). This would require some non trivial code writing but it could eliminate almost all allocations.  
  
Some of the allocations come from Asio and there's not much we can do about that. Asio has been used to write very high performance servers, so I'm not sure that what you are experiencing will be a problem in practice. That said, the current implementation in Beast does not try to be the fastest but tries to be correct, and flexible in terms of interface. Its not intentionally slow either (light optimization passes have been made to make sure nothing outright unreasonable is taking place). This will likely change in the future as the library matures, at that point more investment in optimization seems worthwhile.

---

## Comment 28

> Username: WojciechMigda  
> Created at: 2017-05-24 10:48:32 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303689004  

What you did so far helped me a lot. Thank you for that (both buffers usage and boost 1.58 support).  
As for the allocations, knowing that pretty much this is a baseline for the time being I may leave it like that. I will definitely try custom allocations for boost.asio in general, to get a grip of the technique and have some working examples should I decide to dive deeper later on.  
  
Re: "Call async_read_frame in a loop instead of calling async_read", was this code in the `buffers` branch I tried yesterday?  
  
Last but not least, based on your experience can you think of any production ready open source project which uses boost.asio that can be used by beginners like me to learn correct and efficient usage patterns?

---

## Comment 29

> Username: vinniefalco  
> Created at: 2017-05-24 14:19:09 UTC  
> Url: https://github.com/boostorg/beast/issues/376#issuecomment-303738185  

Glad to hear you got some good results!  
  
>...was this code in the buffers branch I tried yesterday?  
  
Nope. You have to write that yourself. Just call `async_read_frame` instead of `async_read` and in your completion handler if the `fin` field of the `frame_info` is `false` then call `async_read_frame` again.  
  
>can you think of any production ready open source project which uses boost.asio that can be used by beginners like me to learn correct and efficient usage patterns  
  
The best place to start is with the examples that come with Asio.
