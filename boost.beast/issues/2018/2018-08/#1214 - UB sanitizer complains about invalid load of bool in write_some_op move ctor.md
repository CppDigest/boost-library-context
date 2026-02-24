# #1214 - UB sanitizer complains about invalid load of bool in write_some_op move ctor [Closed]

> Username: BitonicEelis  
> Created at: 2018-08-02 13:40:32 UTC  
> Updated at: 2018-09-24 12:32:34 UTC  
> Closed at: 2018-09-18 17:23:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1214  

### Version of Beast  
  
#define BOOST_BEAST_VERSION 174  
  
### Steps necessary to reproduce the problem  
  
Compile and run the following program with `-fsanitize=undefined`:  
  
    #include <thread>  
    #include <boost/beast/core.hpp>  
    #include <boost/asio/ip/tcp.hpp>  
    #include <boost/beast/websocket.hpp>  
    #include <boost/asio/connect.hpp>  
  
    namespace asio = boost::asio;  
    using tcp = asio::ip::tcp;  
    namespace beast = boost::beast;  
    namespace http = beast::http;  
  
    void server()  
    {  
        asio::io_context ioc_;  
        tcp::acceptor acceptor_{ioc_};  
  
        auto const address = asio::ip::make_address("127.0.0.1");  
        tcp::endpoint endpoint{address, 17880};  
  
        acceptor_.open(endpoint.protocol());  
        acceptor_.bind(endpoint);  
        acceptor_.listen(asio::socket_base::max_listen_connections);  
  
        tcp::socket sock{ioc_};  
        acceptor_.accept(sock);  
  
        beast::flat_buffer buf;  
        http::request<http::string_body> req_{};  
        http::read(sock, buf, req_);  
  
        assert(beast::websocket::is_upgrade(req_));  
  
        beast::websocket::stream<tcp::socket> ws(std::move(sock));  
        ws.accept(req_);  
  
        struct Bla { void on_write() {} };  
        auto bla = std::make_shared<Bla>();  
  
        std::string tx_ = "bla";  
        std::cout << "BEFORE WRITE" << std::endl;  
        ws.async_write(asio::buffer(tx_), std::bind(&Bla::on_write, bla));  
        std::cout << "AFTER WRITE" << std::endl;  
        std::exit(0);  
    }  
  
    void client()  
    {  
        asio::io_context ioc_{};  
        beast::websocket::stream<tcp::socket> stream_{ioc_};  
        tcp::resolver resolver{ioc_};  
  
        auto const results = resolver.resolve("127.0.0.1", "17880");  
        asio::connect(stream_.next_layer(), results.begin(), results.end());  
        stream_.handshake("127.0.0.1", "/");  
        sleep(2);  
    }  
  
    int main()  
    {  
        std::thread poll_thread{server};  
        sleep(1);  
        client();  
    }  
  
Output:  
  
    BEFORE WRITE  
    /usr/include/boost/beast/websocket/impl/write.ipp:161:5: runtime error: load of value 127, which is not a valid value for type 'bool'  
    AFTER WRITE  
  
### All relevant compiler information  
  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 7.3.0-16ubuntu3' --with-bugurl=file:///usr/share/doc/gcc-7/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++ --prefix=/usr --with-gcc-major-version-only --with-as=/usr/bin/x86_64-linux-gnu-as --with-ld=/usr/bin/x86_64-linux-gnu-ld --program-suffix=-7 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --enable-default-pie --with-system-zlib --with-target-system-zlib --enable-objc-gc=auto --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 7.3.0 (Ubuntu 7.3.0-16ubuntu3)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-02 14:03:10 UTC  
> Updated at: 2018-08-02 14:03:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1214#issuecomment-409937347  

Hmmm..... I am skeptical that this is a defect in Beast because the Travis CI scripts run the tests under ubsan, asan, and valgrind for every commit, and I do not see any errors. This is write.ipp line 161 for version 174:  
https://github.com/boostorg/beast/blob/b7cc754b2a8b8803698c90094aa3852621999093/include/boost/beast/websocket/impl/write.ipp#L161  
  
The runtime error seems to happen on a move-construction of the write composed operation. I note that you aren't calling `ioc_.run()` in `server`. This narrows down the problem considerably. There is exactly one move performed in the call chain containing the initiating function.  
  
Perhaps the issue is an uninitialized boolean data member on line 155, try initializing it and see what happens:  
  
```  
    bool more_ = false;  
```  
https://github.com/boostorg/beast/blob/b7cc754b2a8b8803698c90094aa3852621999093/include/boost/beast/websocket/impl/write.ipp#L155  
  
Thanks

---

## Comment 2

> Username: BitonicEelis  
> Created at: 2018-08-02 14:04:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1214#issuecomment-409937699  

Yes, that change fixes the problem.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-02 14:07:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1214#issuecomment-409938686  

I will fix this in Beast, but I do consider this to be a false positive since there is no actual undefined behavior (and fixing it will incur a very tiny but non-zero runtime penalty with no corresponding runtime benefit).

---

## Comment 4

> Username: BitonicEelis  
> Created at: 2018-08-02 14:07:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1214#issuecomment-409938845  

I understand. Thanks a lot!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-09-03 16:33:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1214#issuecomment-418157888  

This will be fixed in Boost 1.69
