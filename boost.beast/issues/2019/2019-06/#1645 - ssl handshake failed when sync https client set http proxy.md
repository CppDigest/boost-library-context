# #1645 - ssl handshake failed when sync https client set http proxy [Closed]

> Username: allen2015555  
> Created at: 2019-06-28 04:15:59 UTC  
> Updated at: 2019-08-01 01:52:57 UTC  
> Closed at: 2019-08-01 01:52:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1645  

### Version of Beast  
boost 1.68  
  
### Steps necessary to reproduce the problem  
I'm trying to add proxy for example/http/client/sync-ssl. like this:  
https://stackoverflow.com/questions/49837044/problems-making-a-sync-http-request-through-proxy-using-boost-beast  
  
when connect proxy success, the ssl::stream handshake will throw a error. It just happened in the prxoy havel username and passwork. When I use Fiddler for proxy host, everything were success.  
  
For examle:  
  
```  
#include <boost/asio.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/beast.hpp>  
#include <base64.hpp>  
#include <boost/format.hpp>  
  
using std::string;  
using tcp = boost::asio::ip::tcp;    // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl;    // from <boost/asio/ssl.hpp>  
namespace http = boost::beast::http; // from <boost/beast/http.hpp>  
  
#pragma comment(lib, "libssl32MTd.lib")  
#pragma comment(lib, "libcrypto32MTd.lib")  
#pragma comment(lib, "Crypt32.lib")  
  
string Base64Proxy(string name, string pwd)  
{  
    boost::format fmt("%1%:%2%");  
    fmt % name % pwd;  
    return base64_encode(fmt.str());  
}  
  
int main()  
{  
    try {  
        auto const target = "www.boost.org:443";  
        auto const host = "www.boost.org";  
        int version = 11;  
        boost::asio::io_context ioc;  
        ssl::context ctx{ ssl::context::sslv23_client };  
        tcp::resolver resolver{ ioc };  
        ssl::stream<tcp::socket> stream{ ioc, ctx };  
        stream.next_layer().connect({ boost::asio::ip::address_v4::from_string(/* proxy host */), /*proxy port*/ });  
        //stream.next_layer().connect({ boost::asio::ip::address_v4::from_string("127.0.0.1"), 8080 }); // Fiddler porxy  
        {  
            http::request<http::string_body> req1{ http::verb::connect, target, version };  
            req1.set(http::field::host, target);  
            req1.set(http::field::proxy_connection, "keep-alive");  
            req1.set(http::field::proxy_authorization, Base64Proxy(/*proxy username*/, /*proxy password*/));  
            http::write(stream.next_layer(), req1);  
            boost::beast::flat_buffer buffer;  
            http::response<http::empty_body> res;  
            http::parser<false, http::empty_body> p(res);  
            p.skip(true);  
            http::read(stream.next_layer(), buffer, p);  
            std::cout << res << std::endl;  
        }  
  
        // Perform the SSL handshake  
        stream.set_verify_mode(ssl::verify_none);  
        stream.handshake(ssl::stream_base::client); // this will throw " handshake: stream truncated "  
  
        // Set up an HTTP GET request message  
        {  
            http::request<http::string_body> req{ http::verb::get, "/users/license.html", version };  
            req.set(http::field::host, host);  
            req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
            // Send the HTTP request to the remote host  
            http::write(stream, req);  
  
            boost::beast::flat_buffer buffer;  
            http::response<http::dynamic_body> res;  
            http::read(stream, buffer, res);  
            // Write the message to standard out  
            std::cout << res << std::endl;  
        }  
  
        getchar();  
  
        // Gracefully close the stream  
        boost::system::error_code ec;  
        stream.next_layer().shutdown(boost::asio::ip::tcp::socket::shutdown_send);  
        if (ec == boost::asio::error::eof) {  
            // Rationale:  
            // http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
            ec.assign(0, ec.category());  
        }  
        if (ec)  
            throw boost::system::system_error{ ec };  
        // If we get here then the connection is closed gracefully  
    }  
    catch (std::exception const &e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
}  
```  
  
Anybody know why it failed in handshake?  
Thanks!

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-07-28 09:46:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1645#issuecomment-515748695  

This issue has been open for a while with no activity, has it been resolved?
