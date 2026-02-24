# #321 - Cannot resolve local ip adddress on Windows [Closed]

> Username: tt4g  
> Created at: 2020-01-22 06:00:50 UTC  
> Updated at: 2020-12-30 01:12:37 UTC  
> Closed at: 2020-12-30 01:12:36 UTC  
> Url: https://github.com/boostorg/asio/issues/321  

Thank you for great library.  
  
I want to get the IP address of the local machine by `boost::asio::ip::tcp::resolver::resolve()`.  
The documentation says that we can get local endpoints if specify an empty string for host argument: https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/ip__basic_resolver/resolve/overload10.html  
However, I cannot resolve local endpoints on Windows 10 (1903) and Windows Server 2016.  
  
Running the following snippet on Windows 10 will always get "error code: 11001".  
  
```cpp  
#include <boost/asio.hpp>  
#include <boost/system/error_code.hpp>  
  
#include <iostream>  
#include <string>  
  
int main(int, char**)  
{  
    using namespace boost::asio;  
  
    io_context context;  
    ip::tcp::resolver resolver(context);  
  
    const auto protocol = ip::tcp::v4();  
    std::string host = "";  
    std::string service = "";  
    const auto flags = ip::tcp::resolver::flags::address_configured;  
    boost::system::error_code ec;  
  
    for (auto &&resolveIte : resolver.resolve(protocol, host, service, flags, ec))  
    {  
        std::cout << resolveIte.endpoint().address().to_string() << "\n";  
    }  
  
    if (ec) {  
        std::cerr << "error code: " << std::to_string(ec.value()) << "\n";  
    }  
  
    return 0;  
}  
```  
  
I checked the Boost.Asio source code and found out why it could not be resolved.  
It was due to the Win32 API `getaddrinfo()` specification. it is called by `boost::asio::detail::socket_ops::getaddrinfo()`.  
  
Windows Server 2003 and above are not supported specify empty string for pNodeName argument of `getaddrinfo()`  but "..localmachine" works for me.  
  
[getaddrinfo function \(ws2tcpip\.h\) \- Win32 apps \| Microsoft Docs](https://docs.microsoft.com/en-us/windows/win32/api/ws2tcpip/nf-ws2tcpip-getaddrinfo):  
  
> If the pNodeName parameter contains an empty string, all registered addresses on the local computer are returned.  
>  
> On Windows Server 2003 and later if the pNodeName parameter points to a string equal to "..localmachine", all registered addresses on the local computer are returned.  
  
Would you consider adding this annotation to the document?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:12:35 UTC  
> Url: https://github.com/boostorg/asio/issues/321#issuecomment-752293375  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#743](https://github.com/chriskohlhoff/asio/issues/743).
