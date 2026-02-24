# #2119 - Unable to make SSL HTTP working using Conan packaged OpenSSL [Closed]

> Username: gracicot  
> Created at: 2020-11-09 20:41:23 UTC  
> Updated at: 2021-08-20 08:39:43 UTC  
> Closed at: 2021-01-04 02:31:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2119  

### Version of Beast  
  
    #define BOOST_BEAST_VERSION 300  
  
Found in my installation of boost 1.74  
  
### Steps necessary to reproduce the problem  
  
Here's a modified version of the sync SSL example to leave out the fixed certificates, and also output the OpenSSL errors:  
  
```c++  
#include <boost/asio/ssl.hpp>  
#include <boost/lexical_cast.hpp>  
#include <openssl/cryptoerr.h>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/ssl.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/error.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <boost/beast/version.hpp>  
#include <openssl/opensslv.h>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
namespace beast = boost::beast; // from <boost/beast.hpp>  
namespace http = beast::http;   // from <boost/beast/http.hpp>  
namespace net = boost::asio;    // from <boost/asio.hpp>  
namespace ssl = net::ssl;       // from <boost/asio/ssl.hpp>  
using tcp = net::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
// Performs an HTTP GET and prints the response  
int main(int argc, char** argv)  
{  
    std::cout << OPENSSL_VERSION_TEXT << std::endl;  
    try  
    {  
        // Check command line arguments.  
        if(argc != 4 && argc != 5)  
        {  
            std::cerr <<  
                "Usage: http-client-sync-ssl <host> <port> <target> [<HTTP version: 1.0 or 1.1(default)>]\n" <<  
                "Example:\n" <<  
                "    http-client-sync-ssl www.example.com 443 /\n" <<  
                "    http-client-sync-ssl www.example.com 443 / 1.0\n";  
            return EXIT_FAILURE;  
        }  
        auto const host = argv[1];  
        auto const port = argv[2];  
        auto const target = argv[3];  
        int version = argc == 5 && !std::strcmp("1.0", argv[4]) ? 10 : 11;  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // The SSL context is required, and holds certificates  
        ssl::context ctx(ssl::context::tlsv12_client);  
  
        // This holds the root certificate used for verification  
		ctx.set_default_verify_paths();  
  
        // Verify the remote server's certificate  
        ctx.set_verify_mode(ssl::verify_peer);  
  
            // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
        beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(stream.native_handle(), host))  
        {  
            beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
            throw beast::system_error{ec};  
        }  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(stream).connect(results);  
  
        // Perform the SSL handshake  
        stream.handshake(ssl::stream_base::client);  
  
        // Set up an HTTP GET request message  
        http::request<http::string_body> req{http::verb::get, target, version};  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Send the HTTP request to the remote host  
        http::write(stream, req);  
  
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
  
        // Declare a container to hold the response  
        http::response<http::dynamic_body> res;  
  
        // Receive the HTTP response  
        http::read(stream, buffer, res);  
  
        // Write the message to standard out  
        std::cout << res << std::endl;  
  
        // Gracefully close the stream  
        beast::error_code ec;  
        stream.shutdown(ec);  
        if(ec == net::error::eof)  
        {  
            // Rationale:  
            // http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
            ec = {};  
        }  
        if(ec)  
            throw beast::system_error{ec};  
  
        // If we get here then the connection is closed gracefully  
    }  
    catch(boost::system::system_error const& e)  
    {  
        auto const& error = e.code();  
        std::string err = error.message();  
        if (error.category() == boost::asio::error::get_ssl_category()) {  
            err = std::string(" (")  
                +boost::lexical_cast<std::string>(ERR_GET_LIB(error.value()))+","  
                +boost::lexical_cast<std::string>(ERR_GET_FUNC(error.value()))+","  
                +boost::lexical_cast<std::string>(ERR_GET_REASON(error.value()))+") ";  
      
            //ERR_PACK /* crypto/err/err.h */  
            char buf[128];  
            ::ERR_error_string_n(error.value(), buf, sizeof(buf));  
            err += buf;  
            std::cerr << err << std::endl;  
        }  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
  
When running the program like so:  
  
    ./http-client-sync-ssl example.com 443 /  
  
I get this error:  
  
    OpenSSL 1.1.1g  21 Apr 2020  
    (20,367,134) error:1416F086:SSL routines:tls_process_server_certificate:certificate verify failed  
  
My knowledge of SSL and certificate is quite limited, but in this code I assumed that `ctx.set_default_verify_paths()` would load the system's CA certificates. I verified that SSL is working correctly on that machine. If I do `curl https://example.com/` it work without errors.  
  
So if `curl` can do it, certainly asio can too. However, I'm not quite sure how to get there. I tried finding resources about how to get system certificate integrated into boost but found nothing. Even just how to find the right certificate and which files to load for my program to do as well as curl.  
  
Of course, I can disable certificate verification, but I wouldn't do that in production.  
  
I have found a library that act as a simple http client loads additional certificates on windows: https://github.com/RAvenGEr/simple-beast-client/blob/master/include/client_private_ssl.hpp#L37  
  
But I cannot find a linux/osx equivalent.  
  
### All relevant compiler information  
  
I use GCC 10.0.1 in C++20 mode.  
I run my compiler in a docker container based on ubuntu 20.04.  
  
I get the same result compiling the program on MacOS, using GCC 10.0.2.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-11-09 20:45:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724268020  

Hi, are you using the system openssl or one that you built from source?  
  
The code looks right but I will check it out on my linux box and mac in the morning.

---

## Comment 2

> Username: gracicot  
> Created at: 2020-11-09 20:48:24 UTC  
> Updated at: 2020-11-09 21:16:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724269433  

@madmongo1 I am using conan, and I installed version <strike>`1.1.1f`</strike> `1.1.1g`. If that can help you, I can create a small repository with a CMake setup that builds the program in the same configuration I am using.  
  
Here's my conan profile:  
  
```  
[settings]  
arch=x86_64  
arch_build=x86_64  
build_type=Release  
compiler=gcc  
compiler.libcxx=libstdc++11  
compiler.version=10  
os=Linux  
os_build=Linux  
[options]  
[build_requires]  
[env]  
CC=/usr/bin/gcc-10  
CXX=/usr/bin/g++-10  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-11-09 20:53:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724271615  

It's ok. I'll be able to build the cmake file. I'm just wondering whether the default_verify_paths are pointing to the wrong place if the linker is picking up the conan installation of openssl rather than the one installed in ubuntu.  
  
I don't know much about conan - are you able to post the actual command line that invokes the compiler?  
  
i.e. `g++ <options> main.cpp` ?

---

## Comment 4

> Username: gracicot  
> Created at: 2020-11-09 21:15:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724282450  

Firstly a small correction: The OpenSSL version that I installed with conan is `OpenSSL 1.1.1g  21 Apr 2020`. I now output it from my program just to be sure.  
  
The other version is the one installed globally on the container.  
  
Here's the compiler command:  
  
```  
/bin/g++-10 -D_GLIBCXX_USE_CXX11_ABI=1 -isystem /root/.conan/data/boost/1.74.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/include -isystem /root/.conan/data/zlib/1.2.11/_/_/package/19729b9559f3ae196cad45cb2b97468ccb75dcd1/include -isystem /root/.conan/data/bzip2/1.0.8/_/_/package/91a8b22c2c5a149bc617cfc06cdd21bf23b12567/include -isystem /root/.conan/data/openssl/1.1.1g/_/_/package/19729b9559f3ae196cad45cb2b97468ccb75dcd1/include -g -MD -MT src/CMakeFiles/http-beast-ssl.dir/main2.cpp.o -MF src/CMakeFiles/http-beast-ssl.dir/main2.cpp.o.d -o src/CMakeFiles/http-beast-ssl.dir/main2.cpp.o -c ../src/main2.cpp  
```

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-11-09 21:18:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724283910  

Ok I’m going to guess and say that your Conan version of openssl is looking in the wrong directories for the system certificates. Is there a way you can link against the system openssl rather than the Conan one?

---

## Comment 6

> Username: gracicot  
> Created at: 2020-11-09 21:24:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724287396  

Oh I forgot: here's the linking part of the compile command:  
  
```  
/bin/g++-10 -g  src/CMakeFiles/http-beast-ssl.dir/main2.cpp.o -o bin/http-beast-ssl  /root/.conan/data/boost/1.74.0/_/_/package/104297d4d8bdbca7e9351b0f32c32f11628164c7/lib/libboost_system.a  -lrt  /root/.conan/data/zlib/1.2.11/_/_/package/19729b9559f3ae196cad45cb2b97468ccb75dcd1/lib/libz.a  /root/.conan/data/bzip2/1.0.8/_/_/package/91a8b22c2c5a149bc617cfc06cdd21bf23b12567/lib/libbz2.a  /root/.conan/data/openssl/1.1.1g/_/_/package/19729b9559f3ae196cad45cb2b97468ccb75dcd1/lib/libssl.a  /root/.conan/data/openssl/1.1.1g/_/_/package/19729b9559f3ae196cad45cb2b97468ccb75dcd1/lib/libcrypto.a  -ldl  -lpthread  
```  
  
Yes, I can try that. I'll make a small project without conan that uses the system's openssl.  
  
By the way, I made sure my system's certificates are up to date using `update-ca-certificates` and by upgrading the `ca-certificates` package.

---

## Comment 7

> Username: gracicot  
> Created at: 2020-11-10 13:45:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724711175  

@madmongo1 okay, I have some news. When I'm using the system's openssl library, everything is working correctly. I'm really happy that it work. However I would like continuing using a package manager, as I can choose fixed version instead of picking whatever outdated version my distro is packaging. Do you have any clues what's going on that makes the conan version fail to validate certificates? Big big thank you for your support.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-11-10 15:39:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-724782932  

Yes, OpenSSL looks in its build-time configured directories for the system's certificates which are used to verify certificates presented by the TLS server. The conan build has not configured those default paths to be your system's path (normally `/etc/ssl/certs/`).  
  
You'll need to look at the configuration options of the OpenSSL build script, and then figure out how to get conan to build OpenSSL with the correct configuration so that it's looking at your system's certificate store.  
  
Another way to do this is to explicitly read the certificates yourself from `/etc/ssl/certs` and present them to the `ssl::context`.

---

## Comment 9

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-751240909  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: gracicot  
> Created at: 2021-01-04 02:31:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2119#issuecomment-753725533  

This was an issue on Conan side.
