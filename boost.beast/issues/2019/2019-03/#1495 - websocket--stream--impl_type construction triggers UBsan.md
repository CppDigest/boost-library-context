# #1495 - websocket::stream::impl_type construction triggers UBsan [Closed]

> Username: Eelis  
> Created at: 2019-03-03 21:33:27 UTC  
> Updated at: 2019-03-03 22:51:23 UTC  
> Closed at: 2019-03-03 22:51:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1495  

Consider the following program:  
  
    #include <boost/beast/core.hpp>  
    #include <boost/beast/version.hpp>  
    #include <boost/beast/websocket.hpp>  
    #include <boost/asio/connect.hpp>  
    #include <boost/asio/ip/tcp.hpp>  
    #include <thread>  
  
    int main()  
    {  
        namespace net = boost::asio;  
        using net::ip::tcp;  
        namespace beast = boost::beast;  
  
        std::thread t = std::thread([]{  
                net::io_context ioc_;  
                tcp::acceptor acceptor_{ioc_};  
  
                auto const address = net::ip::make_address("127.0.0.1");  
                tcp::endpoint endpoint{address, 17880};  
  
                acceptor_.open(endpoint.protocol());  
                acceptor_.set_option(net::socket_base::reuse_address(true));  
                acceptor_.bind(endpoint);  
                acceptor_.listen(net::socket_base::max_listen_connections);  
  
                tcp::socket socket = acceptor_.accept();  
                beast::websocket::stream<beast::tcp_stream> ws_(beast::tcp_stream(std::move(socket)));  
            });  
  
        net::io_context ioc;  
        tcp::resolver resolver(ioc);  
        beast::tcp_stream stream(ioc);  
        auto const results = resolver.resolve("127.0.0.1", "17880");  
        stream.connect(results);  
  
        t.join();  
    }  
  
Result:  
  
    # g++ -fsanitize=undefined bad.cpp -lpthread && ./a.out  
    /root/boost/boost/beast/websocket/impl/stream_impl.hpp:133:24: runtime error: member call on address 0x7f32e4001140 which does not point to an object of type 'impl_type'  
    0x7f32e4001140: note: object has invalid vptr  
     00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00  
                  ^~~~~~~~~~~~~~~~~~~~~~~  
                  invalid vptr  
  
    # g++ -v  
    Using built-in specs.  
    COLLECT_GCC=g++  
    COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/8/lto-wrapper  
    OFFLOAD_TARGET_NAMES=nvptx-none  
    OFFLOAD_TARGET_DEFAULT=1  
    Target: x86_64-linux-gnu  
    Configured with: ../src/configure -v --with-pkgversion='Ubuntu 8.2.0-7ubuntu1' --with-bugurl=file:///usr/share/doc/gcc-8/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++ --prefix=/usr --with-gcc-major-version-only --program-suffix=-8 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --enable-default-pie --with-system-zlib --with-target-system-zlib --enable-objc-gc=auto --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
    Thread model: posix  
    gcc version 8.2.0 (Ubuntu 8.2.0-7ubuntu1)
