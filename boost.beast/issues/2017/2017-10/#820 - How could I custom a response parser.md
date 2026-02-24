# #820 - How could I custom a response parser? [Closed]

> Username: aikuimail  
> Created at: 2017-10-20 07:51:00 UTC  
> Updated at: 2017-10-23 21:25:10 UTC  
> Closed at: 2017-10-23 21:25:10 UTC  
> Url: https://github.com/boostorg/beast/issues/820  

How could I custom a response parser  
The codes on the boost development document dose not compiled success.  
  
`#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
  
  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
  
using tcp = boost::asio::ip::tcp;         
namespace http = boost::beast::http;   
  
template<  
	bool isRequest,  
	class Body,  
	class Allocator = std::allocator<char>>  
	class custom_parser:public basic_parser<isRequest,  
	custom_parser<isRequest, Body, Allocator>>  
{  
  
};  
  
  
int main(int argc, char** argv)  
{  
	try  
	{  
		auto const host = "192.168.31.163";  
		auto const port = "38888";  
		auto const target = "/api?";  
		std::string paramOn = "c={\"m\":\"on\",\"dt\":{\"ip\":\"192.168.31.176\",\"pcno\":\"DESKTOP-JOSN7F6\",\"netbarid\":\"33\",\"sessionid\":\"815285d7-1ac4-40b8-9829-3840020aadf2\"}}";  
		std::string reponse_data;  
		// The io_service is required for all I/O  
		boost::asio::io_service ios;  
  
		// These objects perform our I/O  
		tcp::resolver resolver{ ios };  
		tcp::socket socket{ ios };  
  
		// Look up the domain name  
		auto const lookup = resolver.resolve({ host, port });  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::connect(socket, lookup);  
  
		// Set up an HTTP GET request message  
		http::request<http::string_body> req{ http::verb::get, target+paramOn, 11 };  
		req.set(http::field::host, host);  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
		// Send the HTTP request to the remote host  
		http::write(socket, req);  
  
		// This buffer is used for reading and must be persisted  
		boost::beast::flat_buffer buffer;  
  
		// Declare a container to hold the response  
		http::response<http::dynamic_body> res;  
  
		// Receive the HTTP response  
		http::read(socket, buffer, res);  
  
		// Write the message to standard out  
		std::cout << res << std::endl;  
  
		// Gracefully close the socket  
		boost::system::error_code ec;  
		socket.shutdown(tcp::socket::shutdown_both, ec);  
  
		// not_connected happens sometimes  
		// so don't bother reporting it.  
		//  
		if (ec && ec != boost::system::errc::not_connected)  
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
  
  
the error is   
>d:\codes\parser\parser\main.cpp(20): error C2143: syntax error: missing ',' before '<'  
1>  d:\codes\parser\parser\main.cpp(24): note: see reference to class template instantiation 'custom_parser<isRequest,Body,Allocator>' being compiled  
1>  d:\libs\boost_1_65_1\boost\beast\core\string.hpp(110): note: see reference to class template instantiation 'boost::basic_string_view<char,std::char_traits<char>>' being compiled  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
  
the same is:  
`template<bool isRequest>  
class custom_parser  
:public basic_parser<isRequest, custom_parser<isRequest>>  
{  
private:  
friend class basic_parser<isRequest, custom_parser>;  
void  
on_request_impl(  
boost::beast::http::verb method,                // The method verb, verb::unknown if no match  
boost::string_view method_str,     // The method as a string  
boost::string_view target,         // The request-target  
int version,                // The HTTP-version  
boost::system::error_code& ec)  
{  
  
}  
  
/// Called after receiving the start-line (isRequest == false).  
void  
on_response_impl(  
int code,                   // The status-code  
boost::string_view reason,         // The obsolete reason-phrase  
int version,                // The HTTP-version  
boost::system::error_code& ec)  
{  
  
}  
  
/// Called after receiving a header field.  
void  
on_field_impl(  
boost::beast::http::field f,                    // The known-field enumeration constant  
boost::string_view name,           // The field name string.  
boost::string_view value,          // The field value  
boost::system::error_code& ec)  
{  
  
}  
  
/// Called after the complete header is received.  
void  
on_header_impl(  
boost::system::error_code& ec)  
{  
  
}  
  
/// Called just before processing the body, if a body exists.  
void  
on_body_init_impl(  
boost::optional<  
std::uint64_t> const&  
content_length,     // Content length if known, else `boost::none`  
boost::system::error_code& ec)  
{  
  
}  
std::size_t  
on_body_impl(  
boost::string_view s,              // A portion of the body  
boost::system::error_code& ec)  
{  
  
}  
  
  
void  
on_chunk_header_impl(  
std::uint64_t size,         // The size of the upcoming chunk,  
// or zero for the last chunk  
boost::string_view extension,      // The chunk extensions (may be empty)  
boost::system::error_code& ec)  
{  
  
}  
  
  
std::size_t  
on_chunk_body_impl(  
std::uint64_t remain,  
boost::string_view body,           // The next piece of the chunk body  
boost::system::error_code& ec)  
{  
  
}  
void  
on_finish_impl(boost::system::error_code& ec)  
{  
  
}  
  
public:  
custom_parser() = default;  
};`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-20 13:17:12 UTC  
> Updated at: 2017-10-20 13:17:52 UTC  
> Url: https://github.com/boostorg/beast/issues/820#issuecomment-338203342  

>The codes on the boost development document dose not compiled success.  
  
All of the code in the documentation compiles successfully under MSVC, gcc, and clang, it gets built as part of the testing through continuous integration:  
https://github.com/boostorg/beast/blob/develop/example/doc/http_examples.hpp#L882  
https://github.com/boostorg/beast/blob/develop/test/doc/http_examples.cpp#L10

---

## Comment 2

> Username: aikuimail  
> Created at: 2017-10-21 09:12:07 UTC  
> Url: https://github.com/boostorg/beast/issues/820#issuecomment-338377372  

#include <boost/beast.hpp>  
#include <iostream>  
  
template <bool isRequest>  
class custom_parser:public basic_parser<isRequest, custom_parser<isRequest>>  
{  
};  
  
just these codes also indicated errors:  
  
1>d:\boost_1_65_1\boost\beast\core\detail\base64.hpp : warning C4819: The file contains a character that cannot be represented in the current code page (936). Save the file in Unicode format to prevent data loss  
1>d:\codes\beast_research\beast_research\http_example.hpp(6): error C2143: syntax error: missing ',' before '<'  
1>  d:\codes\beast_research\beast_research\http_example.hpp(8): note: see reference to class template instantiation 'custom_parser<isRequest>' being compiled  
1>  d:\boost_1_65_1\boost\beast\core\string.hpp(110): note: see reference to class template instantiation 'boost::basic_string_view<char,std::char_traits<char>>' being compiled  
  
help me

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-21 09:29:51 UTC  
> Updated at: 2017-10-21 09:30:07 UTC  
> Url: https://github.com/boostorg/beast/issues/820#issuecomment-338378335  

I have no idea what is going on there. This is the first I have ever seen of this. How did you get the Beast sources? What version of Visual Studio? Try including all the information on the issue submission guidelines: https://github.com/boostorg/beast/blob/develop/.github/ISSUE_TEMPLATE.md

---

## Comment 4

> Username: aikuimail  
> Created at: 2017-10-21 09:33:21 UTC  
> Url: https://github.com/boostorg/beast/issues/820#issuecomment-338378516  

okay, dear vinniefalco, I have just solved this problem by using using namespace boost::beast::http;

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-10-23 21:25:10 UTC  
> Url: https://github.com/boostorg/beast/issues/820#issuecomment-338801384  

It looks like this has been resolved so I will go ahead and close the issue, feel free to open a new one if you get stuck again!
