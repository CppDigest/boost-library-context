# #2241 - Send Real request to server [Closed]

> Username: ghost  
> Created at: 2021-05-24 20:31:58 UTC  
> Updated at: 2022-09-24 05:19:59 UTC  
> Closed at: 2022-09-24 05:19:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2241  

Hello, i am new in Boost:beast i want sent a real request: ((node(51.249,7.148,51.251,7.152); <;);out meta;) to :https://lz4.overpass-api.de/api/interpreter. and get reponse of this request.any idea please.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-24 20:37:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847318549  

Do any of the examples do something close to what you want?

---

## Comment 2

> Username: ghost  
> Created at: 2021-05-24 20:56:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847328732  

I want to sent this " ((node(51.249,7.148,51.251,7.152); <;);out meta;)" to the host that i do in the code in get the reponse i don t now how  
  
```  
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
  
// Performs an HTTP GET and prints the response  
int main(int argc, char** argv)  
{  
    try  
    {  
        // Check command line arguments.  
        if(argc != 4 && argc != 5)  
        {  
            std::cerr <<  
                "Usage: http-client-sync <host> <port> <target> [<HTTP version: 1.0 or 1.1(default)>]\n" <<  
                "Example:\n" <<  
                "    http-client-sync www.example.com 80 /\n" <<  
                "    http-client-sync www.example.com 80 / 1.0\n";  
            return EXIT_FAILURE;  
        }  
        auto const host = https://lz4.overpass-api.de/api/interpreter;  
        auto const port = 443;  
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
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-24 21:00:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847330693  

change this line:  
```  
    http::request<http::string_body> req{http::verb::get, target, version};  
```  
to this  
```  
    http::request<http::string_body> req{http::verb::post, target, version};  
```  
  
and after this line:  
```  
    req.set(http::field::host, host);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
```  
add this:  
```  
    req.body("((node(51.249,7.148,51.251,7.152); <;);out meta;)");  
```

---

## Comment 4

> Username: ghost  
> Created at: 2021-05-24 21:06:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847333797  

Thanks i will test that

---

## Comment 5

> Username: ghost  
> Created at: 2021-05-24 22:22:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847390310  

I go this error in this line```req.body("((node(51.249,7.148,51.251,7.152); <;);out meta;)");``` the compiler don t inderstand the request.  
>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C3861: 'node': identifier not found  
1>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C2143: syntax error: missing ')' before ';'  
1>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C2059: syntax error: '<'  
1>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C2059: syntax error: ')'  
1>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C2065: 'out': undeclared identifier  
1>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C2146: syntax error: missing ';' before identifier 'meta'  
1>C:\Sources\modele\Interfaces\ClientREST\Coeur\CRT_Requete.cpp(24): error C2065: 'meta': undeclared identifier  
1>    0 Warning(s)  
1>    7 Error(s)  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-05-24 23:06:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847410964  

Are you new to C++ ?

---

## Comment 7

> Username: ghost  
> Created at: 2021-05-25 06:03:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847563255  

yes why

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-05-25 12:27:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847825832  

Looks like you've missed out a quote somewhere.  
You should have a reasonably good understanding of C++ before using Asio and Beast.

---

## Comment 9

> Username: ghost  
> Created at: 2021-05-25 13:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847850935  

No the query inside the ```req.body```  is a overpass query language```req.body((node(51.249,7.148,51.251,7.152); <;);out meta;);```  
for that the compiler generate error. the question have you any idea howa to use overpass query language in c++

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-05-25 13:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847860580  

You need to put the "overpass query language statement in quotes don't you? I assume you're sending it to the web server for processing?  
  
`req.body("(node(51.249,7.148,51.251,7.152); <;);out meta;");`

---

## Comment 11

> Username: ghost  
> Created at: 2021-05-25 13:28:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-847870328  

Yes i want to sent that query to server that i montioned in the host, it s a server Overpass API. I tried in quotes but  got that error:  
```error C2661: 'boost::beast::http::message<true,boost::beast::http::string_body,boost::beast::http::fields>::body': no overloaded function takes 1 arguments```

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-05-25 17:32:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848074056  

My mistake:  
  
https://www.boost.org/doc/libs/1_76_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/body/overload1.html  
  
It should be:  
```  
req.body() = "((node(51.249,7.148,51.251,7.152); <;);out meta;)";  
```

---

## Comment 13

> Username: ghost  
> Created at: 2021-05-25 20:49:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848253590  

Thanks ,but now i got this error ``` error: C++ exception with description "end of stream" thrown in the test body.```

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-05-25 20:57:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848259134  

Great, so the program compiled and is working. The server has closed the stream (the reason for which I have no chance of discerning in an issue tracker) and the resulting uncaught exception has caused the program to terminate, as per the c++ standard.  
  
So far, all correct.  
  
is there a specific Beast-related query I can help with?

---

## Comment 15

> Username: ghost  
> Created at: 2021-05-25 21:19:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848272728  

yes, the programe is compiled now but the probleme is here  
```http::request<http::string_body> req{ http::verb::post, target, version };  
  req.set(http::field::host, flux.host);  
  req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  req.body()= "(node(51.249, 7.148, 51.251, 7.152);< ;);out meta;";  
  beast::error_code ec;  
  http::write(flux.stream, req, ec);  
```

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-05-26 05:29:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848469035  

- To be honest, that message looks as if it's been produced by a google test suite.   
- What makes you think the problem is "here"?

---

## Comment 17

> Username: madmongo1  
> Created at: 2021-05-26 06:34:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848504243  

Why do you think the exception is thrown by the write?  
It seems to me more likely that it was thrown by the read, in response to the server closing the connection instead of sending a response.

---

## Comment 18

> Username: ghost  
> Created at: 2021-05-26 06:38:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848506510  

it was probléme in number of port, i make a wrong number.but i got error message in the response  
  
```<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en" lang="en">  
<head>  
  <meta http-equiv="content-type" content="text/html; charset=utf-8" lang="en"/>  
  <title>OSM3S Response</title>  
</head>  
<body>  
  
<p>The data included in this document is from www.openstreetmap.org. The data is made available under ODbL.</p>  
<p><strong style="color:#FF0000">Error</strong>: encoding error: Your input contains only whitespace. </p>  
  
</body>  
</html>  
  
0  
  
```

---

## Comment 19

> Username: madmongo1  
> Created at: 2021-05-26 07:09:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848525275  

OK, Boost.Asio and Boost.Beast are doing what they are supposed to be doing.   
I have no idea about how your web server should behave.  
You will need to look up the error message "encoding error: Your input contains only whitespace" on the openstreetmap documentation and/or consult their help/community.

---

## Comment 20

> Username: ghost  
> Created at: 2021-05-26 09:48:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848631346  

I think the probleme is in buffer size the didin t support the size of data response

---

## Comment 21

> Username: madmongo1  
> Created at: 2021-05-26 10:52:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-848669986  

Beast defaults the response buffer size limit to 8MB.  
You can increase it with a call to:  
  
```  
std::size_t required_bytes = 64 * 1024 * 1024;  // 64 MB  
response.body_limit(required_bytes);  
```  
  
or you can specify unlimited with:  
  
```  
response.body_limit(boost::none);  
```  
  
Never do this on a server, but on a client where you trust the server, it's acceptable.

---

## Comment 22

> Username: ghost  
> Created at: 2021-05-27 06:25:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-849363375  

The probleme was not in server overpass api, i just ad the ```req.prepare_payload();``` after ```  req.body()= "(node(51.249, 7.148, 51.251, 7.152);< ;);out meta;";``` and i got the same reponse that i got in overpassturbo.

---

## Comment 23

> Username: madmongo1  
> Created at: 2021-05-28 02:58:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-850074904  

great news!

---

## Comment 24

> Username: ghost  
> Created at: 2021-06-02 14:15:03 UTC  
> Updated at: 2021-06-02 14:19:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-853065286  

There is a methode to  do our own message of exception ?

---

## Comment 25

> Username: stale[bot]  
> Created at: 2022-01-09 04:16:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2241#issuecomment-1008226710  

This issue has been open for a while with no activity, has it been resolved?
