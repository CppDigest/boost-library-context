# #874 - Beast seems to require an unreleased version of Boost/Asio [Closed]

> Username: Solrax  
> Created at: 2017-11-07 16:42:06 UTC  
> Updated at: 2018-04-05 17:37:10 UTC  
> Closed at: 2017-11-21 01:14:01 UTC  
> Url: https://github.com/boostorg/beast/issues/874  

Beast Version 132  
  
I get the following errors trying to build Beast examples with Microsoft Visual Studio 2015:  
  
websocket_server_sync.cpp  
C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/type_traits.hpp(261): error C2065: 'is_const_buffer_sequence': undeclared identifier  
  C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/type_traits.hpp(264): note: see reference to class template instantiation 'boost::beast::detail::is_all_const_buffer_sequence<B1,Bn...>' being compiled  
C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/type_traits.hpp(261): error C2975: '_Val': invalid template argument for 'std::integral_constant', expected compile-time constant expression  
  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\include\xtr1common(21): note: see declaration of '_Val'  
C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/type_traits.hpp(261): error C2143: syntax error: missing ',' before '&&'  
C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/type_traits.hpp(268): error C2143: syntax error: missing ',' before '<'  
  C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/type_traits.hpp(270): note: see reference to class template instantiation 'boost::beast::detail::is_all_const_buffer_sequence<B,>' being compiled  
C:\boost_1_65_1\boost_1_65_1\libs\beast\include\boost/beast/core/detail/bind_handler.hpp(14): fatal error C1083: Cannot open include file: 'boost/asio/associated_allocator.hpp': No such file or directory  
  
The missing reference for 'std::integral_constant' is a missing boost::asio template - boost::asio::is_const_buffer_sequence  
  
The best clue is the missing boost/asio/associated_allocator.hpp   
  
I have the latest release Boost - 1_65_1 - this include file is not present in the asio library.  
  
It *is* present in the current stand-alone version of Asio at https://github.com/chriskohlhoff/asio/  
But this does not use the Boost namespace or include file paths (at least not according to his documentation at https://think-async.com/)   
  
But I have not been able to find it in any released version of Boost.   
  
If a specific version of Boost is required, it would be helpful to document this in your otherwise very detailed readme.  I cannot locate a branch of Beast that predates this dependency, Github says master and develop are at the same point.

---

## Comment 1

> Username: djarek  
> Created at: 2017-11-07 17:53:01 UTC  
> Url: https://github.com/boostorg/beast/issues/874#issuecomment-342566250  

In order to use Beast with Boost 1.65.1 you'll need to switch to the v124 tag. Versions above that tag have been switched to the new API of ASIO (based on the C++ Networking TS).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-11-13 20:01:38 UTC  
> Url: https://github.com/boostorg/beast/issues/874#issuecomment-344040136  

Has this issue been resolved?

---

## Comment 3

> Username: RedaBousbah  
> Created at: 2017-11-20 09:56:34 UTC  
> Url: https://github.com/boostorg/beast/issues/874#issuecomment-345646077  

Could you please report this on the instruction. Because if it is said to use Boost someone (like me) will download and compile the last version, and if there is no information about this it is a problem.  
Thank you very much!

---

## Comment 4

> Username: scm-ns  
> Created at: 2018-04-05 17:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/874#issuecomment-379017035  

I was having the same build issue with beast. I switched to boost 1.66, and build the beast lib off of the master version and it seems to build fine. I didn't have to switch to v124. In fact, when I did, compilation fails by saying it cannot bind asio/bind_executor.   
  
Note : I have a boost subfolder, and added beast to that subfolder. This is my compile command.   
  
g++ -std=c++11 -Ilib http_server_async.cpp -L/usr/lib/x86_64-linux-gnu -lboost_system -lboost_filesystem -pthread
