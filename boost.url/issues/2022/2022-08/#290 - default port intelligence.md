# #290 - default port intelligence [Closed]

> Username: vinniefalco  
> Created at: 2022-08-02 02:09:26 UTC  
> Updated at: 2022-08-18 16:05:53 UTC  
> Closed at: 2022-08-18 16:05:53 UTC  
> Url: https://github.com/boostorg/url/issues/290  

The library needs to be smart about default ports for the well-known schemes.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-13 16:16:16 UTC  
> Url: https://github.com/boostorg/url/issues/290#issuecomment-1214183975  

```  
int default_port( scheme id ) noexcept;  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-08-13 16:18:26 UTC  
> Updated at: 2022-08-13 16:39:20 UTC  
> Url: https://github.com/boostorg/url/issues/290#issuecomment-1214184252  

A possible use case:  
  
```  
    auto url     = boost::urls::url_view(urlstr);  
    auto [scheme, port] = infer_scheme_and_port(url);  
  
    // build the appropriate websocket stream type depending on whether the URL  
    // indicates a TCP or TLS transport  
    auto result = infer_tls(scheme)  
                      ? std::make_unique< websock_connection >(  
                            ssl::stream< tcp::socket >(ex, sslctx))  
                      : std::make_unique< websock_connection >(tcp::socket(ex));  
  
    // connect the underlying socket of the websocket stream to the first  
    // reachable resolved endpoint  
    auto host = [&]  
    {  
        switch (url.host_type())  
        {  
        case boost::urls::host_type::ipv4:  
        case boost::urls::host_type::ipv6:  
        case boost::urls::host_type::name:  
        {  
            auto host = url.encoded_hostname();  
            if (host.contains('%'))  
                throw std::runtime_error(  
                    "We're not dealing with %-encoded hostnames");  
            return std::string(host.begin(), host.end());  
        }  
        case boost::urls::host_type::ipvfuture:  
            throw std::runtime_error("Unrecognised host type");  
            break;  
        case boost::urls::host_type::none:  
            throw std::runtime_error("No host specified");  
            break;  
        }  
    }();  
    co_await asio::async_connect(  
        result->sock(),  
        co_await resolver.async_resolve(host, std::to_string(port), deferred),  
        deferred);  
  
    // if the connection is TLS, we will want to update the hostname  
    if (auto *tls = result->query_ssl(); tls)  
    {  
        if (!SSL_set_tlsext_host_name(tls->native_handle(), host.c_str()))  
            throw system_error(  
                error_code { static_cast< int >(::ERR_get_error()),  
                             asio::error::get_ssl_category() });  
        co_await tls->async_handshake(ssl::stream_base::client, deferred);  
    }  
```  
  
And possible implementation:  
```  
bool  
infer_tls(boost::urls::scheme scheme)  
{  
    switch (scheme)  
    {  
    case boost::urls::scheme::https:  
    case boost::urls::scheme::wss:  
        return true;  
    default:  
        return false;  
    }  
}  
  
unsigned short  
infer_port(boost::urls::scheme scheme)  
{  
    using enum boost::urls::scheme;  
  
    switch (scheme)  
    {  
    case https:  
    case wss:  
        return 443;  
    case http:  
    case ws:  
        return 80;  
    case ftp:  
        return 21;  
    case file:  
        throw std::runtime_error("file scheme does not have a default port");  
    default:  
        throw std::logic_error("infer_port - scheme not implemented");  
    }  
}  
  
boost::urls::scheme  
infer_scheme(unsigned short port)  
{  
    if (port == 443)  
        return boost::urls::scheme::https;  
    else if (port == 21)  
        return boost::urls::scheme::ftp;  
    else  
        return boost::urls::scheme::http;  
}  
  
std::tuple< boost::urls::scheme, unsigned short >  
infer_scheme_and_port(boost::urls::url_view u)  
{  
    if (u.has_port())  
    {  
        auto port = u.port_number();  
        if (u.has_scheme())  
        {  
            return std::make_tuple(u.scheme_id(), port);  
        }  
        else  
        {  
            return std::make_tuple(infer_scheme(port), port);  
        }  
    }  
    else  
    {  
        if (u.has_scheme())  
        {  
            auto scheme = u.scheme_id();  
            return std::make_tuple(scheme, infer_port(scheme));  
        }  
        else  
        {  
            return std::make_tuple(infer_scheme(80), 80);  
        }  
    }  
}  
```

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-08-14 10:53:52 UTC  
> Url: https://github.com/boostorg/url/issues/290#issuecomment-1214337213  

```cpp  
int default_port( scheme id ) noexcept;  
```  
  
looks good because it already implies we consider the schemes in `scheme` to be the most important ones.
