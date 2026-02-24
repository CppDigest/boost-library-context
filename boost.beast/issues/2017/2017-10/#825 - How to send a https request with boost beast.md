# #825 - How to send a https request with boost beast [Closed]

> Username: aikuimail  
> Created at: 2017-10-23 01:42:36 UTC  
> Updated at: 2017-11-27 22:06:54 UTC  
> Closed at: 2017-11-27 22:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/825  

Dear vinniefalco:  
                         I really love this library, but I don't know how to send a https with the beast, the server dose not have a certificate with the https

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-23 03:20:05 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-338537902  

There are HTTPS examples for both clients and servers in the example/ directory, have you tried them? Here: https://github.com/boostorg/beast/tree/develop/example/http

---

## Comment 2

> Username: aikuimail  
> Created at: 2017-10-23 05:09:03 UTC  
> Updated at: 2017-10-23 05:17:01 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-338549243  

`#include "example/common/root_certificates.hpp"  
  
  
  
#include <boost/beast/core.hpp>  
  
#include <boost/beast/http.hpp>  
  
#include <boost/beast/version.hpp>  
  
#include <boost/asio/connect.hpp>  
  
#include <boost/asio/ip/tcp.hpp>  
  
#include <boost/asio/ssl/stream.hpp>  
  
#include <cstdlib>  
  
#include <iostream>  
  
#include <string>  
  
#pragma comment(lib, "libeay32")  
#pragma comment(lib, "ssleay32")  
  
  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
  
  
										// Performs an HTTP GET and prints the response  
  
int main(int argc, char** argv)  
  
{  
  
	try  
  
	{  
  
		// Check command line arguments.  
  
		auto const host = "192.168.31.175";  
		auto const port = "8443";  
		auto const target = "/NetCafe/servlet/ConsoleBasicServiceServlet";  
		std::string paramOn = "?operator=mofei&serviceid=50025&param=mofei|GjVWhIsUjqFCHVTfJ2IuNw==|0|0|0|0|0";  
		// The io_service is required for all I/O  
  
		boost::asio::io_service ios;  
  
  
  
		// The SSL context is required, and holds certificates  
  
		ssl::context ctx{ ssl::context::sslv23_client };  
  
  
  
		// This holds the root certificate used for verification  
  
		load_root_certificates(ctx);  
  
  
  
		// These objects perform our I/O  
  
		tcp::resolver resolver{ ios };  
  
		ssl::stream<tcp::socket> stream{ ios, ctx };  
  
  
  
		// Look up the domain name  
  
		auto const lookup = resolver.resolve({ host, port });  
  
  
  
		// Make the connection on the IP address we get from a lookup  
  
		boost::asio::connect(stream.next_layer(), lookup);  
  
  
  
		// Perform the SSL handshake  
  
		stream.handshake(ssl::stream_base::client);  
  
  
  
		// Set up an HTTP GET request message  
  
		http::request<http::string_body> req{ http::verb::get, target+paramOn, 11 };  
  
		req.set(http::field::host, host);  
  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
  
  
		// Send the HTTP request to the remote host  
  
		http::write(stream, req);  
  
  
  
		// This buffer is used for reading and must be persisted  
  
		boost::beast::flat_buffer buffer;  
  
  
  
		// Declare a container to hold the response  
  
		http::response<http::dynamic_body> res;  
  
  
  
		// Receive the HTTP response  
  
		http::read(stream, buffer, res);  
  
  
  
		// Write the message to standard out  
  
		std::cout << res << std::endl;  
  
  
  
		// Gracefully close the stream  
  
		boost::system::error_code ec;  
  
		stream.shutdown(ec);  
  
		if (ec == boost::asio::error::eof)  
  
		{  
  
			// Rationale:  
  
			// http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
  
			ec.assign(0, ec.category());  
  
		}  
  
		if (ec)  
  
			throw boost::system::system_error{ ec };  
  
  
  
		// If we get here then the connection is closed gracefully  
  
	}  
  
	catch (std::exception const& e)  
  
	{  
  
		std::cerr << "Error: " << e.what() << std::endl;  
  
		return EXIT_FAILURE;  
  
	}  
  
	return EXIT_SUCCESS;  
  
}`  
  
  
  
Error: handshake: dh key too small

---

## Comment 3

> Username: aikuimail  
> Created at: 2017-10-23 05:44:12 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-338553373  

the server dose not have a certificate with the https！

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-23 12:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-338640053  

The beat example only comes with a couple of root certificates. Most likely your server was signed with a root certificate that is not one that comes with the example code. You will need to do more sophisticated certificate verification. Unfortunately that is beyond the scope of the beast examples.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-23 12:19:22 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-338640639  

If you're going to quote code please use triple backqoutes ```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-11-18 20:54:43 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-345470516  

Has this issue been resolved?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-11-27 22:06:51 UTC  
> Url: https://github.com/boostorg/beast/issues/825#issuecomment-347344693  

It looks like there is nothing more than I can do to help with this issue so I will go ahead and close it. Feel free to create a new issue if you continue to encounter difficulties. Also, when quoting code, consider surrounding it with triple backquotes (```) in order that it be correctly formatted.
