# #428 - Add async_detect_ssl example [Closed]

> Username: vinniefalco  
> Created at: 2017-06-06 13:00:49 UTC  
> Updated at: 2017-06-12 04:03:38 UTC  
> Closed at: 2017-06-12 04:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/428  

An `async_detect_ssl` example of writing a composed operation would be quite handy. It would be nice to also provide `detect_ssl`:  
```  
/** Determine if TLS handshake is requested on a stream.  
  
    This function reads a few bytes on the stream to determine if a TLS (e.g. OpenSSL) handshake  
    is initiated by the remote host. This could be used to allow a listening socket to offer both  
    **http** and **https** on the same port.  
  
    @param stream The stream to read from.  
  
    @param buffer The buffer to store the received data. If this function returns `true`, this  
    buffer should be provided to `boost::asio::ssl::stream::accept` if a TLS handshake is desired.  
    Otherwise the octets should be consumed as if they were received normally.  
  
    @param ec Set to the error, if any occurred.  
  
    @return `true` if a TLS handshake is requested.  
*/  
template<  
    class SyncReadStream,  
    class DynamicBuffer>  
bool  
detect_ssl(  
    SyncReadStream& stream,  
    DynamicBuffer& buffer,  
    error_code& ec);  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-12 04:03:38 UTC  
> Url: https://github.com/boostorg/beast/issues/428#issuecomment-307685418  

Done
