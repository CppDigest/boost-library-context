# #299 - RC_1_2 branch fails to build with Boost 1.72.0 [Closed]

> Username: jbeich  
> Created at: 2019-11-12 12:44:12 UTC  
> Updated at: 2019-11-12 12:44:36 UTC  
> Closed at: 2019-11-12 12:44:36 UTC  
> Url: https://github.com/boostorg/asio/issues/299  

Regressed by https://github.com/boostorg/asio/commit/935dad16cb75  
  
```  
$ c++ --version  
FreeBSD clang version 9.0.0 (tags/RELEASE_900/final 372316) (based on LLVM 9.0.0)  
Target: x86_64-unknown-freebsd13.0  
Thread model: posix  
InstalledDir: /usr/bin  
```  
```c++  
FAILED: CMakeFiles/torrent-rasterbar.dir/src/http_connection.cpp.o  
/usr/bin/c++  -DBOOST_ASIO_ENABLE_CANCELIO -DBOOST_ASIO_HAS_STD_CHRONO -DBOOST_EXCEPTION_DISABLE -DTORRENT_BUILDING_LIBRARY -DTORRENT_BUILDING_SHARED -DTORRENT_USE_ICONV -DTORRENT_USE_LIBCRYPTO -DTORRENT_USE_OPENSSL -D_FILE_OFFSET_BITS=64 -Dtorrent_rasterbar_EXPORTS -I/path/to/libtorrent/include -I/path/to/libtorrent/ed25519/src -I/usr/local/include -fPIC -fvisibility=hidden -fvisibility-inlines-hidden   -Weverything -Wno-documentation -Wno-c++98-compat-pedantic -Wno-padded -Wno-global-constructors -Wno-exit-time-destructors -Wno-weak-vtables -fexceptions -MD -MT CMakeFiles/torrent-rasterbar.dir/src/http_connection.cpp.o -MF CMakeFiles/torrent-rasterbar.dir/src/http_connection.cpp.o.d -o CMakeFiles/torrent-rasterbar.dir/src/http_connection.cpp.o -c /path/to/libtorrent/src/http_connection.cpp  
In file included from /path/to/libtorrent/src/http_connection.cpp:33:  
In file included from /path/to/libtorrent/include/libtorrent/http_connection.hpp:51:  
In file included from /path/to/libtorrent/include/libtorrent/socket.hpp:55:  
In file included from /usr/local/include/boost/asio/write.hpp:1262:  
/usr/local/include/boost/asio/impl/write.hpp:425:40: error: no type named 'executor_type' in 'libtorrent::aux::socket_type'  
    typedef typename AsyncWriteStream::executor_type executor_type;  
            ~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~  
/usr/local/include/boost/asio/impl/write.hpp:534:7: note: in instantiation of template class 'boost::asio::detail::initiate_async_write_buffer_sequence<libtorrent::aux::socket_type>' requested here  
      detail::initiate_async_write_buffer_sequence<AsyncWriteStream>(s),  
      ^  
/path/to/libtorrent/src/http_connection.cpp:270:3: note: in instantiation of function template specialization 'boost::asio::async_write<libtorrent::aux::socket_type, boost::asio::mutable_buffers_1, std::__1::__bind<void (libtorrent::http_connection::*)(const boost::system::error_code &), std::__1::shared_ptr<libtorrent::http_connection> &, const std::__1::placeholders::__ph<1> &> >' requested here  
                async_write(m_sock, boost::asio::buffer(m_sendbuffer)  
                ^  
1 error generated.  
```

---

## Comment 1

> Username: jbeich  
> Created at: 2019-11-12 12:44:36 UTC  
> Url: https://github.com/boostorg/asio/issues/299#issuecomment-552878031  

Sorry, wrong project. ;)
