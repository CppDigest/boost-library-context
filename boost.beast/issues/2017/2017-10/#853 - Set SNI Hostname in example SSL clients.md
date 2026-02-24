# #853 - Set SNI Hostname in example SSL clients [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 13:13:15 UTC  
> Updated at: 2017-10-31 20:36:59 UTC  
> Closed at: 2017-10-31 20:36:59 UTC  
> Url: https://github.com/boostorg/beast/issues/853  

Otherwise 1/3rd of the hosts on the Internet will fail the SSL handshake:  
  
```  
SSL_set_tlsext_host_name(stream_.native_handle(), hostname.c_str());  
```
