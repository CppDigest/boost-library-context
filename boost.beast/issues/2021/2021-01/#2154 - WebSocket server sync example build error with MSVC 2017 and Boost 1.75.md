# #2154 - WebSocket server sync example  build error with MSVC 2017  and Boost 1.75 [Closed]

> Username: ales-kunst  
> Created at: 2021-01-27 10:11:06 UTC  
> Updated at: 2023-08-26 07:23:24 UTC  
> Closed at: 2021-01-27 12:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2154  

I am using following constellation:  
Boost: 1.75.0  
BOOST_BEAST_VERSION 306  
Visual Studio 2017 15.3.5, Microsoft (R) C/C++ Optimizing Compiler Version 19.11.25508.2 for x64  
  
I have succesfully built boost and also succesfully created all the solution files for beast examples.  
  
When I try to build "websocket-server-sync" project I get the following errors:  
  
`  
...  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/buffer_traits.hpp(123): error C2668: 'boost::asio::buffer_sequence_begin': ambiguous call to overloaded function  
...  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/impl/buffers_cat.hpp(127): error C3546: '...': there are no parameter packs available to expand  
...  
`  
  
Here is the whole output of build:  
`  
1>------ Rebuild All started: Project: ZERO_CHECK, Configuration: Debug x64 ------  
1>Checking Build System  
2>------ Rebuild All started: Project: lib-asio, Configuration: Debug x64 ------  
2>Building Custom Rule C:/drive_d/programming/projects/cpp/3rdparty/boost_1_75_0/libs/beast/CMakeLists.txt  
2>lib_asio.cpp  
2>lib-asio.vcxproj -> C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\libs\beast\build\Debug\lib-asio.lib  
3>------ Rebuild All started: Project: lib-beast, Configuration: Debug x64 ------  
3>Building Custom Rule C:/drive_d/programming/projects/cpp/3rdparty/boost_1_75_0/libs/beast/CMakeLists.txt  
3>lib_beast.cpp  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/buffer_traits.hpp(123): error C2668: 'boost::asio::buffer_sequence_begin': ambiguous call to overloaded function  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/buffer.hpp(399): note: could be 'const boost::asio::const_buffer *boost::asio::buffer_sequence_begin<add_rvalue_reference<_Ty>::type>(const ConstBuffer &,enable_if<std::is_convertible<const add_rvalue_reference<_Ty>::type*,const boost::asio::const_buffer*>::value,void>::type *) noexcept'  
3>        with  
3>        [  
3>            _Ty=const unknown-type &,  
3>            ConstBuffer=add_rvalue_reference<const unknown-type&>::type  
3>        ]  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/buffer.hpp(389): note: or       'const boost::asio::mutable_buffer *boost::asio::buffer_sequence_begin<add_rvalue_reference<_Ty>::type>(const MutableBuffer &,enable_if<std::is_convertible<const add_rvalue_reference<_Ty>::type*,const boost::asio::mutable_buffer*>::value,void>::type *) noexcept'  
3>        with  
3>        [  
3>            _Ty=const unknown-type &,  
3>            MutableBuffer=add_rvalue_reference<const unknown-type&>::type  
3>        ]  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/buffer_traits.hpp(123): note: while trying to match the argument list '(add_rvalue_reference<_Ty>::type)'  
3>        with  
3>        [  
3>            _Ty=const unknown-type &  
3>        ]  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/impl/buffers_cat.hpp(127): note: see reference to alias template instantiation 'buffers_iterator_type<unknown-type>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/http/chunk_encode.hpp(251): note: see reference to class template instantiation 'boost::beast::buffers_cat_view<boost::beast::http::detail::chunk_size,boost::asio::const_buffer,boost::beast::http::chunk_crlf>::const_iterator' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/relationship.hpp(474): note: see reference to class template instantiation 'boost::asio::execution::detail::relationship_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/outstanding_work.hpp(475): note: see reference to class template instantiation 'boost::asio::execution::detail::outstanding_work_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/occupancy.hpp(123): note: see reference to class template instantiation 'boost::asio::execution::detail::occupancy_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/mapping.hpp(604): note: see reference to class template instantiation 'boost::asio::execution::detail::mapping_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/context.hpp(130): note: see reference to class template instantiation 'boost::asio::execution::detail::context_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/bulk_guarantee.hpp(687): note: see reference to class template instantiation 'boost::asio::execution::detail::bulk_guarantee_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/blocking_adaptation.hpp(663): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_adaptation_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/blocking.hpp(836): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<0>' being compiled  
3>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/impl/buffers_cat.hpp(127): error C3546: '...': there are no parameter packs available to expand  
3>Done building project "lib-beast.vcxproj" -- FAILED.  
4>------ Rebuild All started: Project: websocket-server-sync, Configuration: Debug x64 ------  
4>Building Custom Rule C:/drive_d/programming/projects/cpp/3rdparty/boost_1_75_0/libs/beast/example/websocket/server/sync/CMakeLists.txt  
4>websocket_server_sync.cpp  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/buffer_traits.hpp(123): error C2668: 'boost::asio::buffer_sequence_begin': ambiguous call to overloaded function  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/buffer.hpp(399): note: could be 'const boost::asio::const_buffer *boost::asio::buffer_sequence_begin<add_rvalue_reference<_Ty>::type>(const ConstBuffer &,enable_if<std::is_convertible<const add_rvalue_reference<_Ty>::type*,const boost::asio::const_buffer*>::value,void>::type *) noexcept'  
4>        with  
4>        [  
4>            _Ty=const unknown-type &,  
4>            ConstBuffer=add_rvalue_reference<const unknown-type&>::type  
4>        ]  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/buffer.hpp(389): note: or       'const boost::asio::mutable_buffer *boost::asio::buffer_sequence_begin<add_rvalue_reference<_Ty>::type>(const MutableBuffer &,enable_if<std::is_convertible<const add_rvalue_reference<_Ty>::type*,const boost::asio::mutable_buffer*>::value,void>::type *) noexcept'  
4>        with  
4>        [  
4>            _Ty=const unknown-type &,  
4>            MutableBuffer=add_rvalue_reference<const unknown-type&>::type  
4>        ]  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/buffer_traits.hpp(123): note: while trying to match the argument list '(add_rvalue_reference<_Ty>::type)'  
4>        with  
4>        [  
4>            _Ty=const unknown-type &  
4>        ]  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/impl/buffers_cat.hpp(127): note: see reference to alias template instantiation 'buffers_iterator_type<unknown-type>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/http/chunk_encode.hpp(251): note: see reference to class template instantiation 'boost::beast::buffers_cat_view<boost::beast::http::detail::chunk_size,boost::asio::const_buffer,boost::beast::http::chunk_crlf>::const_iterator' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/relationship.hpp(474): note: see reference to class template instantiation 'boost::asio::execution::detail::relationship_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/outstanding_work.hpp(475): note: see reference to class template instantiation 'boost::asio::execution::detail::outstanding_work_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/occupancy.hpp(123): note: see reference to class template instantiation 'boost::asio::execution::detail::occupancy_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/mapping.hpp(604): note: see reference to class template instantiation 'boost::asio::execution::detail::mapping_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/context.hpp(130): note: see reference to class template instantiation 'boost::asio::execution::detail::context_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/bulk_guarantee.hpp(687): note: see reference to class template instantiation 'boost::asio::execution::detail::bulk_guarantee_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/blocking_adaptation.hpp(663): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_adaptation_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/asio/execution/blocking.hpp(836): note: see reference to class template instantiation 'boost::asio::execution::detail::blocking_t<0>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/core/impl/buffers_cat.hpp(127): error C3546: '...': there are no parameter packs available to expand  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/http/parser.hpp(56): error C2338: BodyReader type requirements not met  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/websocket/impl/handshake.hpp(48): note: see reference to class template instantiation 'boost::beast::http::parser<false,boost::beast::http::basic_string_body<char,std::char_traits<char>,std::allocator<char>>,std::allocator<char>>' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/websocket/impl/handshake.hpp(56): note: see reference to class template instantiation 'boost::beast::websocket::stream<NextLayer,deflateSupported>::handshake_op<Handler>::data' being compiled  
4>C:\drive_d\programming\projects\cpp\3rdparty\boost_1_75_0\boost/beast/websocket/impl/handshake.hpp(185): note: see reference to class template instantiation 'boost::beast::websocket::stream<NextLayer,deflateSupported>::handshake_op<Handler>' being compiled  
4>Done building project "websocket-server-sync.vcxproj" -- FAILED.  
========== Rebuild All: 2 succeeded, 2 failed, 0 skipped ==========  
`  
P.S.: I have also tried this with Boost 1.74.0 and I get the same errors.   
Is this known issue with the MSVS 2017 and I should use MSVS 2019?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-27 10:56:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-768205195  

Oh boy.  
If you can, please do move to MSCV2019. It's a lot more reliable.  
  
I have reported bugs in MSVC2017 to Microsoft but they have responded to the effect that they won't fix them and advise upgrading the compiler.  
  
In this paritcualr case, we had to jump through a lot of hoops to get this code to compile on a number of older MSVC compilers.  
  
If upgrading is a problem, let me know and I'll devote some time to helping you.

---

## Comment 2

> Username: ales-kunst  
> Created at: 2021-01-27 12:34:32 UTC  
> Updated at: 2021-01-27 12:36:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-768256076  

You are my savior 🙏 👍💪. You saved me from going mad from wondering what have I done wrong. I also have put the question on Stackoverflow 🤔. I will put a link into the answer for anybody else to know about this stuff. In the meantime I have also tried to compile this on MSVS 2019 and it works like a charm 👍. On my company laptop I can have only MSVS 2017 and wanted to try to compile it there and it did not work. No problem. I will use MSVS 2019 on my personal computer.  
You do not need to do anything regarding this issue.   
Thanks again for fast reply.

---

## Comment 3

> Username: ales-kunst  
> Created at: 2021-01-27 12:49:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-768263399  

@madmongo1 Just for information, I have added answer to my question on Stackoverflow (link [here](https://stackoverflow.com/a/65919472/3193255)). If you wish that I change something in the answer please let me know.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-01-27 15:19:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-768357358  

Beast should support the latest Visual Studio 2017. But it looks like you're using an older version.

---

## Comment 5

> Username: pdimov  
> Created at: 2021-01-27 15:19:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-768357480  

19.11.25508.2 is an old version; my VS2017 reports 19.16.27045.

---

## Comment 6

> Username: ales-kunst  
> Created at: 2021-01-27 19:41:23 UTC  
> Updated at: 2021-01-27 19:47:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-768530057  

@vinniefalco, @pdimov  Yes you are right I am using an old version of MSVS 2017 and I can not upgrade it because it is being controlled by our company. But it does not bother me so much because I will use Boost Beast for my personal project and I will build it on my personal computer. I was just desperate because I thought that I did something wrong in the build process and I did not know what. You do not need to worry about it. I have also updated my answer on the Stackoverflow (link [here](https://stackoverflow.com/a/65919472/3193255)).  
Thanks for taking time for answers.

---

## Comment 7

> Username: victorvadl  
> Created at: 2023-08-21 12:24:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-1686230933  

@ales-kunst Im using MSVS 2022 and im having this issue only with boost/beast as you can see in print, can you help me plz?  
  
![image](https://github.com/boostorg/beast/assets/99365539/4232808e-ea13-4e4c-9fe4-a8f388e6c8ff)

---

## Comment 8

> Username: ales-kunst  
> Created at: 2023-08-26 07:23:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2154#issuecomment-1694210562  

> @ales-kunst Im using MSVS 2022 and im having this issue only with boost/beast as you can see in print, can you help me plz?  
>   
> ![image](https://user-images.githubusercontent.com/99365539/262022137-4232808e-ea13-4e4c-9fe4-a8f388e6c8ff.png)  
  
Hi @victorvadl ,  
  
Sorry for my late response. I think that you did not set include path to boost Beast library correctly inside your MSVS correctly.  
  
Cheers,  
Ales
