# #2974 - BEAST ssl client examples don't seem to check peer certificate subject [Closed]

> Username: Mario-Klebsch  
> Created at: 2025-01-21 16:54:38 UTC  
> Updated at: 2025-02-11 16:14:00 UTC  
> Closed at: 2025-02-11 16:13:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2974  

I tried to use the beast exaple async http client wirh ssl (http_client_async_ssl.cpp).  
  
I was able to get it running and connect to my server. But when I experimented with different certificates, I found, that it seems to not check the peer certificate subject.  
  
The source code does call SSL_set_tlsext_host_name() to set the hostname used in the service name indication, but I cannot find any call to SSL_set1_host(), which is used to tell openssl what hostname to expect in the peer certificate.  
  
When I add a call to SSL_set1_host() to session::run(), the hostname verification works for me:  
  
```  
	if(! SSL_set_tlsext_host_name(stream_.native_handle(), sni))  
	{  
		beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
		std::cerr << "SSL_set_tlsext_host_name(" << sni <<  ")" << ec.message() << "\n";  
		return;  
	}  
  
	if (! SSL_set1_host(stream_.native_handle(), sni))  
	{  
		beast::error_code ec{static_cast<int>(::ERR_get_error()), net::error::get_ssl_category()};  
		std::cerr << "SSL_set1_host(" << sni <<  ")" << ec.message() << "\n";  
		return;  
	}  
```  
  
IMHO, checking the peers identity is crucial for using TLS, and the call to SSL_set1_host() should be included to all ssl eamples.  
  
In fact, I think, checking the peers identity is that essential, that methods for both calls should be added to boost::asio::ssl::stream.  
  
73, Mario
