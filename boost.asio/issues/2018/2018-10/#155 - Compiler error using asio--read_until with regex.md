# #155 - Compiler error using asio::read_until with regex [Closed]

> Username: KevinRausch  
> Created at: 2018-10-21 19:40:44 UTC  
> Updated at: 2020-12-30 00:53:58 UTC  
> Closed at: 2020-12-30 00:53:57 UTC  
> Url: https://github.com/boostorg/asio/issues/155  

The following will compile on 1.65.1 but not on newer versions. Tested with VS2013u5 and VS2017u7  
  
```c++  
#include <boost/asio/io_service.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/read_until.hpp>  
#include <boost/asio/streambuf.hpp>  
#include <boost/regex.hpp>  
  
void main(int argc, char ** argv)  
{  
    boost::asio::io_service io_service;  
    boost::asio::ip::tcp::socket device(io_service);  
    boost::asio::streambuf streambuf;  
    boost::system::error_code error;  
    boost::regex until;  
  
    auto bytes = boost::asio::read_until(device, streambuf, until, error);  
}  
```  
  
```  
1>------ Rebuild All started: Project: Test2013, Configuration: Debug x64 ------  
1>main.cpp  
1>.\test_code\libs\boost\boost\asio\impl\read_until.hpp(273): error C2664: 'size_t boost::asio::basic_streambuf<std::allocator<char>>::read_size_helper(boost::asio::basic_streambuf<std::allocator<char>> &,size_t)': cannot convert argument 1 from 'boost::asio::basic_streambuf_ref<Allocator>' to 'boost::asio::basic_streambuf<std::allocator<char>> &'  
1>        with  
1>        [  
1>            Allocator=std::allocator<char>  
1>        ]  
1>.\test_code\libs\boost\boost\asio\impl\read_until.hpp(401): note: see reference to function template instantiation 'size_t boost::asio::read_until<SyncReadStream,boost::asio::basic_streambuf_ref<Allocator>>(SyncReadStream &,DynamicBuffer &&,const boost::regex &,boost::system::error_code &)' being compiled  
1>        with  
1>        [  
1>            SyncReadStream=boost::asio::ip::tcp::socket,  
1>            Allocator=std::allocator<char>,  
1>            DynamicBuffer=boost::asio::basic_streambuf_ref<std::allocator<char>>  
1>        ]  
1>.\test_code\test\main.cpp(15): note: see reference to function template instantiation 'size_t boost::asio::read_until<boost::asio::ip::tcp::socket,std::allocator<char>>(SyncReadStream &,boost::asio::basic_streambuf<std::allocator<char>> &,const boost::regex &,boost::system::error_code &)' being compiled  
1>        with  
1>        [  
1>            SyncReadStream=boost::asio::ip::tcp::socket  
1>        ]  
1>Done building project "Test2013.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========  
```

---

## Comment 1

> Username: KevinRausch  
> Created at: 2018-10-21 19:49:45 UTC  
> Url: https://github.com/boostorg/asio/issues/155#issuecomment-431698350  

Should have checked Trac too. Duplicate of [Trac #13291: https://svn.boost.org/trac10/ticket/13291](https://svn.boost.org/trac10/ticket/13291)

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:53:56 UTC  
> Url: https://github.com/boostorg/asio/issues/155#issuecomment-752290049  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#645](https://github.com/chriskohlhoff/asio/issues/645).
