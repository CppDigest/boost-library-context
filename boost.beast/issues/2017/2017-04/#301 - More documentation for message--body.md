# #301 - More documentation for message::body [Open]

> Username: ADDubovik  
> Created at: 2017-04-11 09:22:30 UTC  
> Updated at: 2022-01-20 18:42:48 UTC  
> Url: https://github.com/boostorg/beast/issues/301  

Could you please add an example of using HTTP POST with Beast?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-11 14:16:24 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293277214  

Setting up an HTTP POST request is no different than filling out any other request message type, except that you set the method to POST and put in whatever query parameters and/or message body is appropriate:  
```  
    beast::http::request<beast::http::string_body> req;  
    req.method = "POST";  
    req.url = "/cgi-bin/post.php?userid=fc92ab5234";  
    req.version = 11;  
    req.fields.insert("Host", "example.com");  
    req.fields.insert("User-Agent", "Beast");  
    req.body = "Post, world!";  
```  
  
However, what I think you're really asking, is for the details on how to post to a particular CGI script. Server or script-specific features are beyond the scope of Beast's examples and documentation, there are plenty of resources on the world wide web that can help you. On the Beast side you just have to remember to put the "query parameters" in the `url` field, and the message body in the `body` field of the request. You might also consider studying the HTTP RFC: https://tools.ietf.org/html/rfc7230

---

## Comment 2

> Username: ADDubovik  
> Created at: 2017-04-11 14:26:18 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293280374  

Thanks, this example seems to be what I need. And I think it's good idea to put them to "examples" folder, because there's really no information on the web about using POST with Beast.  
Also there's rather confusing code in message.ipp ln 182 and ln 202.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-11 14:40:40 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293284848  

Why is the code confusing? POST requests must specify a Content-Length even if the message body is zero octets in size, this comes straight from rfc7230, I urge you to familiarize yourself with it.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-04-11 14:41:12 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293285005  

`prepare` in message.ipp line 202 could use a comment linking to the specific section of rfc7230.

---

## Comment 5

> Username: ADDubovik  
> Created at: 2017-04-11 14:52:47 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293288942  

if(msg.fields.exists("Content-Length"))  
        throw make_exception<std::invalid_argument>(  
            "prepare called with Content-Length field set", __FILE__, __LINE__);  
  
My first impression was that there's no support of POST in Beast.  
  
When I have seen ln 202 - I understood that there's such a support.  
But how to use it with Beast, is not a trivial thing, because there's no "req.body" usage in any example.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-04-11 15:36:25 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293302921  

I see, there's no body usage in the example code. There are some examples in the documentation though:  
http://vinniefalco.github.io/beast/beast/http/body.html

---

## Comment 7

> Username: ADDubovik  
> Created at: 2017-04-11 15:50:39 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293307448  

... but there's nothing useful in the "request.body" description:  
https://vinniefalco.github.io/beast/beast/ref/http__message/body.html  
  
And also, example you get is the example of response.body. I need no response body using POST.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-04-11 15:52:15 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293307915  

The `body` field is the same for requests and responses.

---

## Comment 9

> Username: ADDubovik  
> Created at: 2017-04-11 15:55:46 UTC  
> Updated at: 2017-04-11 15:56:05 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293309010  

I understand this. But usage of request and response is not the same.  
  
As I see, the documentation is a weak side of Beast. There's a lot of abstractions and templates means high flexibility. But I think it would be great if you show this flexibility in the documentation and examples.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-04-11 16:01:01 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293310780  

Its beyond the scope of the documentation to explain the difference in usage between requests and responses. Beast HTTP is a low-level library. It only tries to do two things: 1. model HTTP messages, and 2. serialize and deserialize those messages (i.e. read/write on sockets). It doesn't know anything about POST, Content-Encoding, Accept, proxies, etc... beyond what is necessary to correctly parse and send messages as per rfc7230.  
  
Beast assumes users are familiar with Boost.Asio, rfc7230, and the usage of HTTP protocol.

---

## Comment 11

> Username: ADDubovik  
> Created at: 2017-04-11 16:13:47 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293314638  

> It doesn't know anything about POST  
  
Really?  
Message.ipp line 201:  
if(*pi.content_length > 0 ||  
                        ci_equal(msg.method, "POST"))  
  
> Beast assumes users are familiar with Boost.Asio, rfc7230, and the usage of HTTP protocol.  
1. There's a mnemonics problem:  
https://github.com/vinniefalco/Beast/issues/171  
People who know RFC will be confused.  
2. RFC knowledge helps to make right HTTP request manually, but will not help to use Beast to make such a request.

---

## Comment 12

> Username: ADDubovik  
> Created at: 2017-04-11 16:39:45 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293322267  

Am I right that Beast should give me an easy way to use HTTP?  
If so, there's should be an easy way to create HTTP requests - more easy than making such a request manually. And this way should be explained in the documentation.  
  
Also, I think it's a good idea to add two things to the documentation:  
- how to see an HTTP request made for me by Beast;  
- how to change something manually in such a request.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-04-11 18:50:10 UTC  
> Updated at: 2017-04-11 18:52:03 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293364828  

>Really? Message.ipp line 201:  
  
You quoted me out of context. You left out _"...beyond what is necessary to correctly parse and send messages."_  
  
>Am I right that Beast should give me an easy way to use HTTP?  
  
No. Beast does not aim to make using HTTP "as easy as possible." It merely aims to make using HTTP "possible." It is a low level library. It is not a replacement for cUrl, and its not a turn-key web server. It is aimed primarily at writers of libraries who will build on top of it to create reusable HTTP components which can interoperate with each other.  
  
That said, very often people who are not library writers will find Beast useful, because it provides functionality not found elsewhere. These users often write their own routines to make putting together HTTP requests easier. Because there are a limitless number of styles in which this can be achieved, Beast does not try to favor a particular style by offering its own.  
  
>there's should be an easy way to create HTTP requests - more easy than making such a request manually.  
  
That is beyond the scope of Beast. But perhaps you can write that functionality on top of it and offer your solution as a library for others!  
  
>how to see an HTTP request made for me by Beast;  
  
All `beast::http::header` and `beast::http::message` objects support streaming to `std::ostream` using `operator<<`, as shown in the HTTP example.  
  
>how to change something manually in such a request.  
  
The `header` and `message` objects are simple structs with public data members, anyone with an understanding of C++ should know how to perform assignments! It sounds like you want help doing something specific, but unfortunately that is beyond the scope of the library.  
  
If you want help with something specific, like how to POST a form to a specific cgi script, go ahead and open up an issue and I will try to answer it for you (or maybe someone else will). If you have a program that you can paste, that would also be helpful.  
  
>People who know RFC will be confused.  
  
I believe the current set of names strikes the best balance between adherance to the RFC, terseness, and readability. But If you think you can come up with better names, I would love to hear them. You can open an issue and offer suggestions, you can participate in the Boost formal review scheduled for the first week of July (see the boost-dev mailing list), or both.

---

## Comment 14

> Username: ADDubovik  
> Created at: 2017-04-11 19:30:17 UTC  
> Updated at: 2017-04-11 19:30:35 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293375319  

> You quoted me out of context. You left out "beyond what is necessary to correctly parse and send messages."  
  
Sorry, English is not my native language. I did not understood all the sentence.  
  
> But perhaps you can write that functionality on top of it and offer your solution as a library for others!  
  
Yes, I already wrote such a library for use in my soft. It seems to be reusable, and may be I will provide it an open-source.  
  
> But If you think you can come up with better names, I would love to hear them.  
  
I think the best way is to use all the RFC names with the same sense.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-04-11 20:48:49 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293395489  

>I think the best way is to use all the RFC names with the same sense.  
  
How is Beast naming different from the RFC? Its practically identical:  
  
RFC: **HTTP-message**  
Beast: `http::message`  
  
RFC: ***( header-field CRLF )**  
Beast: `http::header`  
  
RFC: **field-name ":" OWS field-value OWS**  
Beast: `fields`  
  
Which of these do you propose changing, and what do you suggest as the replacement? Or which other Beast identifiers are confusing or dissimilar?

---

## Comment 16

> Username: vinniefalco  
> Created at: 2017-04-11 20:49:18 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-293395577  

>I think the best way is to use all the RFC names with the same sense.  
  
How is Beast naming different from the RFC? Its practically identical:  
  
RFC: **HTTP-message**  
Beast: `http::message`  
  
RFC: ***( header-field CRLF )**  
Beast: `http::header`  
  
RFC: **field-name ":" OWS field-value OWS**  
Beast: `fields`  
  
Which of these do you propose changing, and what do you suggest as the replacement? Or which other Beast identifiers are confusing or dissimilar?

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-04-28 00:46:30 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-297877397  

Has this issue been resolved? If not, feel free to re-open it. Or create a new issue if you are having another problem - thanks!

---

## Comment 18

> Username: snahmad  
> Created at: 2018-05-16 17:09:20 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-389595021  

I am following this example   
  
https://www.boost.org/doc/libs/develop/libs/beast/example/http/client/sync-ssl/http_client_sync_ssl.cpp  
  
My code to http client post is below. I am getting a bad request response in my http server.  
Is my post request preparation is correct?   
  
```  
//  
// Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP SSL client, synchronous  
//  
//------------------------------------------------------------------------------  
  
#include "root_certificates.hpp"  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/error.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
#include <thread>  
  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl;       // from <boost/asio/ssl.hpp>  
namespace http = boost::beast::http;    // from <boost/beast/http.hpp>  
  
										// Performs an HTTP GET and prints the response  
int main(int argc, char** argv)  
{  
	try  
	{  
		// Check command line arguments.  
		if (argc != 4 && argc != 5)  
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
		boost::asio::io_context ioc;  
  
		// The SSL context is required, and holds certificates  
		ssl::context ctx{ ssl::context::sslv23_client };  
  
		// This holds the root certificate used for verification  
		load_root_certificates(ctx);  
  
		// These objects perform our I/O  
		tcp::resolver resolver{ ioc };  
		ssl::stream<tcp::socket> stream{ ioc, ctx };  
  
		// Set SNI Hostname (many hosts need this to handshake successfully)  
		if (!SSL_set_tlsext_host_name(stream.native_handle(), host))  
		{  
			boost::system::error_code ec{ static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category() };  
			throw boost::system::system_error{ ec };  
		}  
  
		// Look up the domain name  
		auto const results = resolver.resolve(host, port);  
  
		// Make the connection on the IP address we get from a lookup  
		boost::asio::connect(stream.next_layer(), results.begin(), results.end());  
  
		// Perform the SSL handshake  
		stream.handshake(ssl::stream_base::client);  
  
		// Set up an HTTP GET request message  
		http::request<http::string_body> req{ http::verb::post, target, version };  
		req.set(http::field::host, host);  
		req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
		req.body() = std::string("test");  
		req.set(http::field::content_type, "application/json");  
		req.set(http::field::content_length, 4);  
		req.prepare_payload();  
  
		// Send the HTTP request to the remote host  
		http::write(stream, req);  
  
		std::this_thread::sleep_for(std::chrono::seconds(3));  
  
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
}  
```

---

## Comment 19

> Username: vinniefalco  
> Created at: 2018-05-16 17:25:32 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-389600105  

You did not specify the command line you used to invoke the program listing above, nor did you provide the exact contents of the HTTP response that you are receiving.

---

## Comment 20

> Username: snahmad  
> Created at: 2018-05-16 18:34:18 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-389621514  

ok, I will find HTTP bad response string and will give tomorrow.

---

## Comment 21

> Username: snahmad  
> Created at: 2018-05-17 09:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-389805162  

It is working. my mistake,

---

## Comment 22

> Username: researcher2  
> Created at: 2018-06-18 13:38:23 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-398056814  

Hello  
  
After following the quick start in the docs I got as far as changing the request method. Was stuck on the body though so ended up in this ticket after googling, "boost beast post example". Could be very helpful to add a "HTTP Client Post" example under the quickstart which mentions request.body.  
  
Thank You

---

## Comment 23

> Username: zxffffffff  
> Created at: 2019-09-25 07:25:25 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-534890862  

Could be very helpful to add a "HTTP Client Post" example under the quickstart which mentions request.body.  
  
Thank You

---

## Comment 24

> Username: schvarcz  
> Created at: 2022-01-14 12:44:13 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-1013086978  

Hello everyone.  
  
In my opinion, it remains a big issue for new users. I struggled a lot with this until finding this issue report. Also, one thing that might be important to some users, is how to send files through `Beast`.  
  
To anyone else who ended up here, the bottom line is that `Beast` is "just" an interface to a http message. You still have to build the message yourself as the [RFC 1866](https://datatracker.ietf.org/doc/html/rfc1866#section-8.2) and [RFC 2388](https://datatracker.ietf.org/doc/html/rfc2388) define.  
  
If you don't have time to read the RFCs, W3.org is [here](https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4) to help you.  
  
------  
  
If the maintainers of this repo has any interest of adding these examples to Beast`s documentation, please let me know. I would happily prepare a PR with this.  
  
------  
  
I modified the existing [http-client-sync](https://github.com/boostorg/beast/blob/29b5f11623fd2447e95a8b2cc6a9321f42ca5019/example/http/client/sync/http_client_sync.cpp) example to show (i) how a message could be sent by the post method, (ii) how a json message could be transmitted and (iii) how a file could be uploaded using this library.  
  
(i) To send a message by **POST method**:  
```c++//  
//  
// Copyright (c) 2021-2022 Guilherme Schvarcz Franco (guilhermefrancosi at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP client, synchronous, POST method  
//  
//------------------------------------------------------------------------------  
  
//[example_http_client  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
namespace beast = boost::beast;     // from <boost/beast.hpp>  
namespace http = beast::http;       // from <boost/beast/http.hpp>  
namespace net = boost::asio;        // from <boost/asio.hpp>  
using tcp = net::ip::tcp;           // from <boost/asio/ip/tcp.hpp>  
  
// Performs an HTTP POST and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        if(argc != 4 && argc != 5)  
        {  
            std::cerr <<  
                "Usage: http-client-sync-post <host> <port> <target> [<HTTP version: 1.0 or 1.1(default)>]\n" <<  
                "Example:\n" <<  
                "    http-client-sync-post www.example.com 80 /\n" <<  
                "    http-client-sync-post www.example.com 80 / 1.0\n";  
            return EXIT_FAILURE;  
        }  
        auto const host = argv[1];  
        auto const port = argv[2];  
        auto const target = argv[3];  
        int version = argc == 5 && !std::strcmp("1.0", argv[4]) ? 10 : 11;  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
        beast::tcp_stream stream(ioc);  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        stream.connect(results);  
  
        // Set up an HTTP POST request message  
        http::request<http::string_body> req{http::verb::post, target, version};  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        std::string payload = "var1=1&var2=2";  
        req.set(http::field::content_type, "application/x-www-form-urlencoded");  
        req.set(http::field::content_length, payload.size());  
        req.body() = payload;  
  
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
  
        // Gracefully close the socket  
        beast::error_code ec;  
        stream.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
        // not_connected happens sometimes  
        // so don't bother reporting it.  
        //  
        if(ec && ec != beast::errc::not_connected)  
            throw beast::system_error{ec};  
  
        // If we get here then the connection is closed gracefully  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
  
//]  
```  
  
(ii) If you wanna send a json message (not a json file), it is very similar, you just have to change your `content_type` and `payload` to:  
  
```c++  
std::string payload = "{\"m_list\":[1,2,3]}";  
req.set(http::field::content_type, "application/json");  
req.set(http::field::content_length, payload.size());  
req.body() = payload;  
```  
  
(iii) To upload a file using `multipart/form-data`:  
<sup>(again, just the `content-type` and the message change, but here goes a full example app)<sup>  
```c++  
//  
// Copyright (c) 2021-2022 Guilherme Schvarcz Franco (guilhermefrancosi at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP client, synchronous, uploading a file.  
//  
//------------------------------------------------------------------------------  
  
//[example_http_client  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <fstream>  
#include <string>  
#include <sstream>  
  
namespace beast = boost::beast;     // from <boost/beast.hpp>  
namespace http = beast::http;       // from <boost/beast/http.hpp>  
namespace net = boost::asio;        // from <boost/asio.hpp>  
using tcp = net::ip::tcp;           // from <boost/asio/ip/tcp.hpp>  
  
// Performs an HTTP POST with file upload and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        if(argc != 4 && argc != 5)  
        {  
            std::cerr <<  
                "Usage: http-client-sync-form-data <host> <port> <target> <file> [<HTTP version: 1.0 or 1.1(default)>]\n" <<  
                "Example:\n" <<  
                "    http-client-sync-form-data www.example.com 80 /\n" <<  
                "    http-client-sync-form-data www.example.com 80 / 1.0\n";  
            return EXIT_FAILURE;  
        }  
        auto const host = argv[1];  
        auto const port = argv[2];  
        auto const target = argv[3];  
        std::string const file = argv[4];  
        int version = argc == 6 && !std::strcmp("1.0", argv[5]) ? 10 : 11;  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
        beast::tcp_stream stream(ioc);  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        stream.connect(results);  
  
        // Set up an HTTP POST request message with file upload  
        http::request<http::string_body> req{http::verb::post, target, version};  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Prepare the multipart/form-data message  
        std::ifstream f(file);  
        std::stringstream file_buffer;  
        file_buffer << f.rdbuf(); // It may be binary files.  
  
        std::string payload = "--AaB03x\n" // This is the boundary to limit the start/end of a part. It may be any string. More info on the RFC 2388 (https://datatracker.ietf.org/doc/html/rfc2388)  
                              "Content-Disposition: form-data; name=\"comment\"\n"  
                              "\n"  
                              "Larry\n"   
                              "--AaB03x\n"  
                              "Content-Disposition: form-data; name=\"files\"; filename=\"" + file.substr(file.find_last_of("/")+1) + "\"\n"  
                              "Content-Type: application/octet-stream\n"  
                              "\n"  
                              + file_buffer.str() + "\n"  
                              "--AaB03x--\n";  
  
        req.set(http::field::content_type, "multipart/form-data; boundary=AaB03x");  
        req.set(http::field::content_length, payload.size());  
        req.body() = payload;  
  
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
  
        // Gracefully close the socket  
        beast::error_code ec;  
        stream.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
        // not_connected happens sometimes  
        // so don't bother reporting it.  
        //  
        if(ec && ec != beast::errc::not_connected)  
            throw beast::system_error{ec};  
  
        // If we get here then the connection is closed gracefully  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
  
//]  
```  
  
---------  
  
I hope this may help the next person that will end up here.  
  
Cheers

---

## Comment 25

> Username: vinniefalco  
> Created at: 2022-01-14 15:54:20 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-1013244774  

Thank you !!

---

## Comment 26

> Username: schvarcz  
> Created at: 2022-01-14 17:46:53 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-1013329938  

Should I add these examples to a PR, or you prefer to take from here?

---

## Comment 27

> Username: madmongo1  
> Created at: 2022-01-14 18:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-1013353428  

If you’re happy to submit a PR we’d welcome it.

---

## Comment 28

> Username: schvarcz  
> Created at: 2022-01-20 18:42:48 UTC  
> Url: https://github.com/boostorg/beast/issues/301#issuecomment-1017809065  

Very kind from you not taking my happiness from me :).  
  
I just submitted the PR [here](https://github.com/boostorg/beast/pull/2381).  
  
Please, let me know if there is any change you would like me to do.
