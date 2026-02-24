# #980 - SSL connection seems to be closed after exact 100 http requests [Closed]

> Username: Wangxishu  
> Created at: 2018-01-14 04:29:16 UTC  
> Updated at: 2024-12-03 15:26:38 UTC  
> Closed at: 2018-01-15 04:21:03 UTC  
> Url: https://github.com/boostorg/beast/issues/980  

I use synchronized http request, it works well until exact 100 requests.  
Then the connection will be closed. I got a "Short read" error.  
  
This code has been used in two independent websites which have REST apis. Both have the same issue. After I made 100 requests (consistent every time), the connection seems to be closed. If program stops and restart, it can make another 100 requests.  
Any one can help on this ?  
  
thanks,  
  
```  
MYCLASS::MYCLASS() :  
        m_ctx(boost::asio::ssl::context::sslv23_client),  
        m_socket_stream(m_io_context, m_ctx),  
        m_hmac()  
{  
    boost::asio::ip::tcp::resolver resolver(m_io_context);  
  
    // Set SNI Hostname (many hosts need this to handshake successfully)  
    if(! SSL_set_tlsext_host_name(m_socket_stream.native_handle(), rest_host.c_str()))  
    {  
        boost::system::error_code ec{static_cast<int>(::ERR_get_error()), boost::asio::error::get_ssl_category()};  
        throw boost::system::system_error{ec};  
    }  
  
    // Look up the domain name  
    auto const results = resolver.resolve(rest_host, port);  
  
    // Make the connection on the IP address we get from a lookup  
    boost::asio::connect(m_socket_stream.next_layer(), results.begin(), results.end());  
  
    // Perform the SSL handshake  
    m_socket_stream.handshake(ssl::stream_base::client);  
}  
  
std::string MYCLASS::api_reqeust_sync(const std::string& method, API_Privacy privacy, const std::string& parameter)  
    try  
    {  
        namespace http = boost::beast::http;  
        std::string target = method;  
        if(!parameter.empty())  
        {  
            target += parameter;  
        }  
  
        http::request<http::string_body> req{http::verb::get, target, 11};  
        req.set(http::field::host, rest_host);  
        req.set(http::field::content_type, "Application/JSON");  
  
        // Send the HTTP request to the remote host  
        http::write(m_socket_stream, req);  
  
        // This buffer is used for reading and must be persisted  
        boost::beast::flat_buffer buffer;  
  
        // Declare a container to hold the response  
        http::response<http::string_body> res;  
  
        // Receive the HTTP response  
        http::read(m_socket_stream, buffer, res);  
  
        return res.body();  
    }  
    catch(std::exception const& e)  
    {  
        LOG_CRIT << "API request error:  " << e.what();  
        return "";  
    }  
  
```  
int main()  
{  
    MYCLASS class;  
    while(true)  
{  
   sleep(1second);  
   api_reqeust_sync();  
   **// so here, after 100 requests, the connection will be closed ? what did I do wrong ?**  
}  
  
  
  
}

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-14 06:00:55 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-357490844  

Your code looks okay, I am not sure why it isn't working. This looks very much like an SSL related problem and not something specific to Beast. What version of OpenSSL?

---

## Comment 2

> Username: Wangxishu  
> Created at: 2018-01-14 14:53:12 UTC  
> Updated at: 2018-01-15 02:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-357517068  

I am using centos7,   openssl.x86_64    1:1.0.2k-8.el7     
Boost v1.66 with beast  
thanks for your quick response

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-14 23:45:44 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-357553128  

Are you sure that the other end is not doing this on purpose?

---

## Comment 4

> Username: Wangxishu  
> Created at: 2018-01-15 02:52:49 UTC  
> Updated at: 2018-01-15 02:55:36 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-357570429  

pretty sure it is not the other end. I had same feeling and hence used python-request to test and no issues. I've also tried to use openssl.v1.1 and no luck.   
Today, my error was "stream truncated" other than "short read". but so far still stuck with 100 requests  
  
do you think it is related to SSL certification ? do I need to do anything special to certification?  
I've tried to load_root_certificates(ctx); or skip it, the result is the same.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-01-15 03:32:59 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-357574927  

I have no idea what's going on. It has to be something to do with OpenSSL, there's nothing in Beast that would cause that.

---

## Comment 6

> Username: Wangxishu  
> Created at: 2018-01-15 04:21:03 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-357579741  

thanks a lot Vinnie, I'll look into more in OPENSSL

---

## Comment 7

> Username: kazaroff  
> Created at: 2024-12-03 11:34:15 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-2514300659  

Hello @Wangxishu   
I seems to have exact or very similar issue: after sending 100 requests over the same stream `ssl::stream<boost::beast::tcp_stream>` I'm consistently getting `end of stream` on the next request. This is boost-1.75 on Alma Linux 9. My HTTPS server is apache reverse proxying the boost/beast server (non-SSL), but it works nicely over plain TCP, the issue only shows up with SSL on the client.  
Did you manage to understand the issue?  
Thanks!

---

## Comment 8

> Username: ashtum  
> Created at: 2024-12-03 11:51:42 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-2514339162  

> Hello [@Wangxishu](https://github.com/Wangxishu) I seems to have exact or very similar issue: after sending 100 requests over the same stream `ssl::stream<boost::beast::tcp_stream>` I'm consistently getting `end of stream` on the next request. This is boost-1.75 on Alma Linux 9. My HTTPS server is apache reverse proxying the boost/beast server (non-SSL), but it works nicely over plain TCP, the issue only shows up with SSL on the client. Did you manage to understand the issue? Thanks!  
  
The `end of stream` error indicates that the server (Apache reverse proxy), has gracefully closed the connection. This might be due to a timeout configured in the Apache settings.

---

## Comment 9

> Username: kazaroff  
> Created at: 2024-12-03 15:26:36 UTC  
> Url: https://github.com/boostorg/beast/issues/980#issuecomment-2514878694  

Thanks @ashtum you are right, I need to handle a potential graceful disconnect, that is also not trivial with ssl stream, thanks to Vinnie:  
https://groups.google.com/g/boost-list/c/KNpE8Nl9qQ4/m/b3TgyLTdAgAJ
