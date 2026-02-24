# #2059 - beast::flat_stream destructor throws exception in some cases. [Closed]

> Username: ManojNisal  
> Created at: 2020-08-22 09:11:12 UTC  
> Updated at: 2020-08-29 06:58:12 UTC  
> Closed at: 2020-08-29 06:58:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2059  

I'm writing a simple wrapper around the beast async session example at "https://www.boost.org/doc/libs/1_73_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp"  
I have created an HttpsRequest class, which holds a pointer to session object.  
Send() method in HttpsRequest class, sets up the io context, creates a session object and runs the io.  
The response is set in a reference parameter to Send.  
All this works fine.  
  
However, when the session object referred by the session pointer in HttpsRequest class is destroyed, it throws an acces violation acception.  
Note that if session pointer is not stored as a member variable of httpsRequest and its scope is limited to Send() method, then no exception is encountered. As I need access to session pointer in other methods like say SendAsync(), I have kept it as a member variable  
  
The exception thrown in first case is "Exception thrown at 0x00007FF906AF9A9D (ntdll.dll) in SessionTest.exe: 0xC0000005: Access violation writing location 0x0000027DCEAAB8C8. occurred"  
And the call stack is:  
  
	ntdll.dll!RtlEnterCriticalSection()	Unknown  
 	SessionTest.exe!boost::asio::detail::win_mutex::lock() Line 51	C++  
 	SessionTest.exe!boost::asio::detail::scoped_lock<boost::asio::detail::win_mutex>::scoped_lock<boost::asio::detail::win_mutex>(boost::asio::detail::win_mutex & m) Line 47	C++  
 	SessionTest.exe!boost::asio::detail::win_iocp_socket_service_base::destroy(boost::asio::detail::win_iocp_socket_service_base::base_implementation_type & impl) Line 155	C++ 	  
	SessionTest.exe!boost::asio::detail::io_object_impl<boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>,boost::asio::executor>::~io_object_impl<boost::asio::detail::win_iocp_socket_service<boost::asio::ip::tcp>,boost::asio::executor>() Line 118	C++  
 	SessionTest.exe!boost::asio::basic_socket<boost::asio::ip::tcp,boost::asio::executor>::~basic_socket<boost::asio::ip::tcp,boost::asio::executor>() Line 1797	C++  
 	SessionTest.exe!boost::asio::basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>::~basic_stream_socket<boost::asio::ip::tcp,boost::asio::executor>() Line 331	C++  
 	[External Code]	  
 	SessionTest.exe!boost::detail::sp_ms_deleter<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::impl_type>::destroy() Line 67	C++ 	  
	SessionTest.exe!boost::detail::sp_ms_deleter<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::impl_type>::operator()(boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::impl_type * __formal) Line 93	C++  
 	SessionTest.exe!boost::detail::sp_counted_impl_pd<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::impl_type *,boost::detail::sp_ms_deleter<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::impl_type>>::dispose() Line 173	C++  
 	SessionTest.exe!boost::detail::sp_counted_base::release() Line 113	C++  
 	SessionTest.exe!boost::detail::shared_count::~shared_count() Line 427	C++  
 	[External Code]	 	  
	SessionTest.exe!boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>::~basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>() Line 654	C++	  
	SessionTest.exe!boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>::~stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>() Line 120	C++ 		  
	SessionTest.exe!boost::beast::flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>::~flat_stream<boost::asio::ssl::stream<boost::beast::basic_stream<boost::asio::ip::tcp,boost::asio::executor,boost::beast::unlimited_rate_policy>>>() Line 98	C++  
 	[External Code]	  
 	SessionTest.exe!Session::~Session() Line 73	C++  
  
Attaching the relevant full code in a zipped file named SessionTest.zip.  
  
### Version of Beast  
277  
  
### Steps necessary to reproduce the problem  
You can debug through the program in attached file. As soon as the Test() function finishes execution, there's an access violation exception.  
I used Visual Studio 2019 as IDE and the corresponding compiler.  
  
[SessionTest.zip](https://github.com/boostorg/beast/files/5112146/SessionTest.zip)

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-22 09:19:17 UTC  
> Updated at: 2020-08-22 09:19:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678617859  

This kind of thing can happen if you still have an outstanding async IO operation in progress when you delete the websocket stream. It's worth checking that. Thanks for the zip I'll take a look (although a github repo is better as it means I can submit a PR if I fix it).

---

## Comment 2

> Username: ManojNisal  
> Created at: 2020-08-22 09:20:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678618036  

Isn't all async operation supposed to be completed when ioc.run() returns?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-08-22 09:26:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678618557  

sec, let me run up a little project and see if I can replicate

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-08-22 09:35:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678619318  

This won't be the problem, but FYI on gcc:  
  
```  
/home/rhodges/github/test-scenarios/beast-issue-2059/src/main.cpp: In member function ‘void Session::set_range(uint64_t, uint64_t)’:  
/home/rhodges/github/test-scenarios/beast-issue-2059/src/main.cpp:286:14: error: comparison of integer expressions of different signedness: ‘uint64_t’ {aka ‘long unsigned int’} and ‘int’ [-Werror=sign-compare]  
  286 |   if (offset > -1 && numBytes > 0)  
      |       ~~~~~~~^~~~  
```

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-08-22 09:41:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678619771  

After fixing the sign/unsigned compare bug I get this:  
  
```  
/home/rhodges/github/test-scenarios/beast-issue-2059/cmake-build-debug/check  
HTTP/1.1 200 OK  
  
Date: Sat, 22 Aug 2020 09:40:24 GMT  
  
Server: Apache/2.2.15 (CentOS)  
  
X-Powered-By: PHP/5.3.3  
  
Expires: Tue, 22 Sep 2020 09:40:24 +0000  
  
Cache-Control: max-age=2678400  
  
Last-Modified: Fri, 14 Aug 2020 06:01:12 +0000  
  
ETag: "48bee32b7bc0259148ceed1e164fc88d"  
  
Connection: close  
  
Transfer-Encoding: chunked  
  
Content-Type: text/html; charset=UTF-8  
  
  
  
2b9b  
  
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  
  
<html xmlns="http://www.w3.org/1999/xhtml" lang="en" xml:lang="en">  
<head>  
  <meta name="viewport" content="width=device-width,initial-scale=1.0" />  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />  
<title>libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp - 1.73.0</title>  <link rel="icon" href="/favicon.ico" type="image/ico" />  
  <link rel="stylesheet" type="text/css" href="/style-v2/section-doc.css" />  
  <!--[if IE 7]> <style type="text/css"> body { behavior: url(/style-v2/csshover3.htc); } </style> <![endif]-->  
  
</head>  
  
<body>  
  <div id="heading">  
    <div class="heading-inner">  
  <div class="heading-placard"></div>  
  
  <h1 class="heading-title">  
  <a href="/">  
  <img src="/gfx/space.png" alt= "Boost C++ Libraries" class="heading-logo" />  
  <span class="heading-boost">Boost</span>  
  <span class="heading-cpplibraries">C++ Libraries</span>  
  </a></h1>  
  
  <p class="heading-quote">  
  <q>...one of the most highly  
  regarded and expertly designed C++ library projects in the  
  world.</q> <span class="heading-attribution">&mdash; <a href=  
  "http://www.gotw.ca/" class="external">Herb Sutter</a> and <a href=  
  "http://en.wikipedia.org/wiki/Andrei_Alexandrescu" class="external">Andrei  
  Alexandrescu</a>, <a href=  
  "http://safari.awprofessional.com/?XmlId=0321113586" class="external">C++  
  Coding Standards</a></span></p>  
</div>  
  </div>  
  <div class="boost-common-header-notice"><a class="boost-common-header-inner" href="/doc/libs/release/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp">This is the documentation for an old version of Boost.  
                Click here to view this page for the latest version.</a></div>  
  
  <div id="body">  
    <div id="body-inner">  
      <div id="content">  
        <div class="section" id="docs">  
          <div class="section-0">  
            <div class="section-body">  
              <h3>libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp</h3>  
<pre>  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at <a href="http://www.boost.org/LICENSE_1_0.txt">http://www.boost.org/LICENSE_1_0.txt</a>)  
//  
// Official repository: <a href="https://github.com/boostorg/beast">https://github.com/boostorg/beast</a>  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP SSL client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include &quot;example/common/root_certificates.hpp&quot;  
  
#include &lt;<a href="../../../../../../boost/beast/core.hpp">boost/beast/core.hpp</a>&gt;  
#include &lt;<a href="../../../../../../boost/beast/http.hpp">boost/beast/http.hpp</a>&gt;  
#include &lt;<a href="../../../../../../boost/beast/ssl.hpp">boost/beast/ssl.hpp</a>&gt;  
#include &lt;<a href="../../../../../../boost/beast/version.hpp">boost/beast/version.hpp</a>&gt;  
#include &lt;<a href="../../../../../../boost/asio/strand.hpp">boost/asio/strand.hpp</a>&gt;  
#include &lt;cstdlib&gt;  
#include &lt;functional&gt;  
#include &lt;iostream&gt;  
#include &lt;memory&gt;  
#include &lt;string&gt;  
  
namespace beast = boost::beast;         // from &lt;boost/beast.hpp&gt;  
namespace http = beast::http;           // from &lt;boost/beast/http.hpp&gt;  
namespace net = boost::asio;            // from &lt;boost/asio.hpp&gt;  
namespace ssl = boost::asio::ssl;       // from &lt;boost/asio/ssl.hpp&gt;  
using tcp = boost::asio::ip::tcp;       // from &lt;boost/asio/ip/tcp.hpp&gt;  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr &lt;&lt; what &lt;&lt; &quot;: &quot; &lt;&lt; ec.message() &lt;&lt; &quot;\n&quot;;  
}  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this&lt;session&gt;  
{  
    tcp::resolver resolver_;  
    beast::ssl_stream&lt;beast::tcp_stream&gt; stream_;  
    beast::flat_buffer buffer_; // (Must persist between reads)  
    http::request&lt;http::empty_body&gt; req_;  
    http::response&lt;http::string_body&gt; res_;  
  
public:  
    explicit  
    session(  
        net::executor ex,  
        ssl::context&amp; ctx)  
    : resolver_(ex)  
    , stream_(ex, ctx)  
    {  
    }  
  
    // Start the asynchronous operation  
    void  
    run(  
        char const* host,  
        char const* port,  
        char const* target,  
        int version)  
    {  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(stream_.native_handle(), host))  
        {  
            beast::error_code ec{static_cast&lt;int&gt;(::ERR_get_error()), net::error::get_ssl_category()};  
            std::cerr &lt;&lt; ec.message() &lt;&lt; &quot;\n&quot;;  
            return;  
        }  
  
        // Set up an HTTP GET request message  
        req_.version(version);  
        req_.method(http::verb::get);  
        req_.target(target);  
        req_.set(http::field::host, host);  
        req_.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        // Look up the domain name  
        resolver_.async_resolve(  
            host,  
            port,  
            beast::bind_front_handler(  
                &amp;session::on_resolve,  
                shared_from_this()));  
    }  
  
    void  
    on_resolve(  
        beast::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, &quot;resolve&quot;);  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(stream_).async_connect(  
            results,  
            beast::bind_front_handler(  
                &amp;session::on_connect,  
                shared_from_this()));  
    }  
  
    void  
    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type)  
    {  
        if(ec)  
            return fail(ec, &quot;connect&quot;);  
  
        // Perform the SSL handshake  
        stream_.async_handshake(  
            ssl::stream_base::client,  
            beast::bind_front_handler(  
                &amp;session::on_handshake,  
                shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, &quot;handshake&quot;);  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Send the HTTP request to the remote host  
        http::async_write(stream_, req_,  
            beast::bind_front_handler(  
                &amp;session::on_write,  
                shared_from_this()));  
    }  
  
    void  
    on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, &quot;write&quot;);  
  
        // Receive the HTTP response  
        http::async_read(stream_, buffer_, res_,  
            beast::bind_front_handler(  
                &amp;session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, &quot;read&quot;);  
  
        // Write the message to standard out  
        std::cout &lt;&lt; res_ &lt;&lt; std::endl;  
  
        // Set a timeout on the operation  
        beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));  
  
        // Gracefully close the stream  
        stream_.async_shutdown(  
            beast::bind_front_handler(  
                &amp;session::on_shutdown,  
                shared_from_this()));  
    }  
  
    void  
    on_shutdown(beast::error_code ec)  
    {  
        if(ec == net::error::eof)  
        {  
            // Rationale:  
            // <a href="http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error">http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error</a>  
            ec = {};  
        }  
        if(ec)  
            return fail(ec, &quot;shutdown&quot;);  
  
        // If we get here then the connection is closed gracefully  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char** argv)  
{  
    // Check command line arguments.  
    if(argc != 4 &amp;&amp; argc != 5)  
    {  
        std::cerr &lt;&lt;  
            &quot;Usage: http-client-async-ssl &lt;host&gt; &lt;port&gt; &lt;target&gt; [&lt;HTTP version: 1.0 or 1.1(default)&gt;]\n&quot; &lt;&lt;  
            &quot;Example:\n&quot; &lt;&lt;  
            &quot;    http-client-async-ssl www.example.com 443 /\n&quot; &lt;&lt;  
            &quot;    http-client-async-ssl www.example.com 443 / 1.0\n&quot;;  
        return EXIT_FAILURE;  
    }  
    auto const host = argv[1];  
    auto const port = argv[2];  
    auto const target = argv[3];  
    int version = argc == 5 &amp;&amp; !std::strcmp(&quot;1.0&quot;, argv[4]) ? 10 : 11;  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12_client};  
  
    // This holds the root certificate used for verification  
    load_root_certificates(ctx);  
  
    // Verify the remote server's certificate  
    ctx.set_verify_mode(ssl::verify_peer);  
  
    // Launch the asynchronous operation  
    // The session is constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    std::make_shared&lt;session&gt;(  
        net::make_strand(ioc),  
        ctx  
        )-&gt;run(host, port, target, version);  
  
    // Run the I/O service. The call will return when  
    // the get operation is complete.  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
</pre>  
            </div>  
          </div>  
        </div>  
      </div>  
  
      <div class="clear"></div>  
    </div>  
  </div>  
  
  <div id="footer">  
    <div id="footer-left">  
      <div id="revised">  
        <p>Revised $Date$</p>  
      </div>  
  
      <div id="copyright">  
        <p>Copyright Beman Dawes, David Abrahams, 1998-2005.</p>  
  
        <p>Copyright Rene Rivera 2004-2008.</p>  
      </div>  <div id="license">  
    <p>Distributed under the <a href="/LICENSE_1_0.txt" class=  
    "internal">Boost Software License, Version 1.0</a>.</p>  
  </div>  
    </div>  
  
    <div id="footer-right">  
        <div id="banners">  
    <p id="banner-xhtml"><a href="https://validator.w3.org/check?uri=referer"  
    class="external">XHTML 1.0</a></p>  
  
    <p id="banner-css"><a href=  
    "https://jigsaw.w3.org/css-validator/check/referer" class=  
    "external">CSS</a></p>  
  
    <p id="banner-osi"><a href=  
    "https://opensource.org/docs/definition.php" class="external">OSI  
    Certified</a></p>  
  </div>  
    </div>  
  
    <div class="clear"></div>  
  </div>  
</body>  
</html>  
  
  
0  
  
  
  
 result=0  
In ~Session  
  
Process finished with exit code 0  
```

---

## Comment 6

> Username: ManojNisal  
> Created at: 2020-08-22 09:48:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678620479  

Thanks for the prompt response @madmongo1   
So likely the issue is limited only to Windows?

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-08-22 10:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678622781  

I'd be surprised if it was Windows causing the issue. We test on a vast range of hosts and compilers.  
  
I wonder whether there is a subtle lifetime issue. It looks as if your shared_ptr is running its deleter after the io_context has been destroyed.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-08-22 10:19:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678623091  

By the way, if I were you i'd create the io_context and ssl::context in main() and pass them into your functions. This solves most lifetime issues.  
  
Spinning the io_context until it stops is not a scalable way to express sync operations in terms of async. You might be better off having a background thread for sync io work and using a condition_variable to indicate completion of the async operation.

---

## Comment 9

> Username: ManojNisal  
> Created at: 2020-08-22 10:21:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678623232  

But if i were to hide away io and ssl context from a caller, what would be the best way to do that?

---

## Comment 10

> Username: ManojNisal  
> Created at: 2020-08-22 10:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678623319  

By the way, i was planning to use the same class for async call, with slight tweaks.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-08-22 10:36:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678624511  

> But if i were to hide away io and ssl context from a caller, what would be the best way to do that?  
  
It depends on the topology of your application. If you write your objects in terms of executor and reference to ssl::context (or even have an object that contains those two things), you could think of that as an injectable that you pass into all operations (or store in a global if you really want to hide it).  
  
Executors are lightweight and the polymorphic `executor` class (or as of boost 1.74, `any_io_executor`) allow your objects to be written in terms of any Executor model (`strand`, `io_context::executor`, `system_executor` etc) without having to know the details of the execution environment.

---

## Comment 12

> Username: madmongo1  
> Created at: 2020-08-22 10:38:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-678624641  

BTW, as of Boost 1.74 you can #define `BOOST_ASIO_ENABLE_HANDLER_TRACKING` to get asio to log the progress of its async operations and construction/destruction. It may help you to understand where your access violation is coming from.

---

## Comment 13

> Username: ManojNisal  
> Created at: 2020-08-29 06:58:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2059#issuecomment-683247773  

When I ensure that io context and ssl context outlive the session variable, I don't see the unhandled exceptions.  
Thanks for the pointers @madmongo1 .  
Closing this issue.
