# #995 - stream.shutdown( ec )? [Closed]

> Username: VikingExplorer  
> Created at: 2018-01-22 17:34:38 UTC  
> Updated at: 2022-07-21 05:53:54 UTC  
> Closed at: 2018-01-24 13:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/995  

I'm creating a class to encapsulate web service calls using boost beast. I started with the synchronous example: [ssl sync](http://www.boost.org/doc/libs/1_66_0/libs/beast/example/http/client/sync-ssl/http_client_sync_ssl.cpp)  
  
I'm still learning the ropes, so please bear with my ignorance. I wrote a unit test to exercise this functionality. It works well, a call is made, and results are returned from an Azure web service.  
  
However, when I add this line from the sample:   
`      stream.shutdown( ec );`  
  
It appears to hang the unit test. Perhaps it's waiting for a response from the server or a timeout?  
  
So, started researching this issue:  [40177264](https://stackoverflow.com/questions/40177264/making-ssl-connection-using-boost-asio)  [32046034](https://stackoverflow.com/questions/32046034/what-is-the-proper-way-to-securely-disconnect-an-asio-ssl-socket)  [15312219](https://stackoverflow.com/questions/15312219/need-to-call-sslstreamshutdown-when-closing-boost-asio-ssl-socket)  
  
Should I be shutting down the lowest_layer?    
Why is it hanging (or more generally, what is the proper way)?  
  
I thought about asking this question in the Asio section, but my question is really about using Beast & this Sample.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-22 17:49:57 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-359507329  

This is an Asio question, but I don't mind answering it. To cleanly close an SSL connection (and prevent security vulnerabilities) a protocol-level shutdown is needed. You are calling the correct function: `asio::ssl::stream::shutdown`. Attempting to shut down the lowest layer will not perform an SSL shutdown. Instead, it will perform a simple TCP/IP shutdown (by sending a FIN packet). This results in an SSL connection which is not cleanly closed. There may be additional pending information (both user-defined data and protocol handshake data) which has yet to be delivered by the protocol stack which would be interrupted upon an unexpected FIN.  
  
I'm not sure why you are hanging connecting to the remote service. However, I have observed that very often clients and servers do not handle the SSL shutdown correctly. Chrome for example intentionally skips the graceful closure sequence, with a goal of optimization. Web servers sometimes do this as well. For practical purposes you will need to set a timeout on your protocol-level shutdown. As the synchronous interfaces do not provide a way to implement reliable timeouts, you will need to use the asynchronous interfaces. When the timeout occurs, just call `cancel` or `close` on the lowest layer and let the outstanding completion handlers be invoked with `asio::error::operation_aborted`.  
  
Thanks

---

## Comment 2

> Username: VikingExplorer  
> Created at: 2018-01-22 19:39:22 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-359540479  

I think I'm going to switch to the coroutine example. Seems like the best of both worlds.  
  
While I have you, can these lines be executed once, or do they have to be run for each web service call?  
  
```  
    boost::asio::io_context ioc;  
    ssl::context ctx{ssl::context::sslv23_client};  
    load_root_certificates(ctx);  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-22 19:45:27 UTC  
> Updated at: 2018-01-22 19:45:51 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-359542283  

Those root certificates that come with the examples are just examples, you can't be using them in a "real" program. You will need to access the operating system's certificate store and load the ones you find there. Doing so is a non-trivial endeavor and far outside the scope of Beast. I hope to work on a new library which accomplishes this but it is far off.  
  
cpprestsdk has some code to do this, as does Chrome.  
  
Certificates need to be loaded once per `ssl_context`.

---

## Comment 4

> Username: VikingExplorer  
> Created at: 2018-01-22 20:57:21 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-359562016  

wow, I messed up. Hope I don't have to go back to cpprestsdk.    
  
Would this be the cpprest code:  
  
```  
    bool handle_cert_verification(bool preverified, boost::asio::ssl::verify_context &verifyCtx)  
    {  
        // OpenSSL calls the verification callback once per certificate in the chain,  
        // starting with the root CA certificate. The 'leaf', non-Certificate Authority (CA)  
        // certificate, i.e. actual server certificate is at the '0' position in the  
        // certificate chain, the rest are optional intermediate certificates, followed  
        // finally by the root CA self signed certificate.  
  
        const auto &host = utility::conversions::to_utf8string(m_http_client->base_uri().host());  
#if defined(__APPLE__) || (defined(ANDROID) || defined(__ANDROID__))  
        // On OS X, iOS, and Android, OpenSSL doesn't have access to where the OS  
        // stores keychains. If OpenSSL fails we will doing verification at the  
        // end using the whole certificate chain so wait until the 'leaf' cert.  
        // For now return true so OpenSSL continues down the certificate chain.  
        if(!preverified)  
        {  
            m_openssl_failed = true;  
        }  
        if(m_openssl_failed)  
        {  
            return verify_cert_chain_platform_specific(verifyCtx, host);  
        }  
#endif  
  
        boost::asio::ssl::rfc2818_verification rfc2818(host);  
        return rfc2818(preverified, verifyCtx);  
    }  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-01-22 21:31:40 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-359571776  

Yep, that's the code! Both the author and I are on the C++ language Slack, visit https://cpplang.now.sh for an automatic invite. I'm in the **#boost** channel.

---

## Comment 6

> Username: segeter  
> Created at: 2022-07-20 08:14:27 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-1189968534  

Maybe this is the reason: https://github.com/chriskohlhoff/asio/issues/804

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-07-20 13:07:27 UTC  
> Url: https://github.com/boostorg/beast/issues/995#issuecomment-1190265153  

Don't use synchronous websocket if you want something robust that supports timeouts.
