# #2774 - read message error: wrong version number (SSL routines, ssl3_get_record) [Closed]

> Username: romanholidaypancakes  
> Created at: 2023-12-06 18:08:15 UTC  
> Updated at: 2023-12-07 12:51:15 UTC  
> Closed at: 2023-12-07 12:51:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2774  

### Version of Beast  
  
1.83  
  
### Steps necessary to reproduce the problem  
When my client (beast) sends a message to the server, the server can receive the message normally, but when the server replies to the client, the client will read an error:  
```  
wrong version number (SSL routines, ssl3_get_record)  
```  
Did I miss something?  
The client uses beast, and the server uses other projects.  
  
### All relevant compiler information

---

## Comment 1

> Username: ashtum  
> Created at: 2023-12-06 18:15:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1843428143  

Could you please provide more information about your code? Are you connecting through a proxy?

---

## Comment 2

> Username: romanholidaypancakes  
> Created at: 2023-12-06 18:30:02 UTC  
> Updated at: 2023-12-06 18:30:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1843453526  

I'm not using a proxy, the client code is using [websocket_client_async_ssl](https://github.com/boostorg/beast/blob/develop/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp).  
  
The certificate also comes from `load_root_certificates`, and the certificate used by the server comes from `load_server_certificate`  
I loaded it via `SSL_CTX`  
```  
  
    SSL_CTX_set_default_passwd_cb(ctx, [](char* buf, int size, int rwflag, void* userdata) -> int {  
        const char* password = "test";  
        if (password != nullptr && size > static_cast<int>(strlen(password))) {  
            strncpy(buf, password, size);  
            return strlen(password);  
        }  
        return 0;  
    });  
  
  
    SSL_CTX_set_options(ctx, SSL_OP_ALL | SSL_OP_NO_SSLv2 | SSL_OP_SINGLE_DH_USE);  
  
  
    if (SSL_CTX_use_certificate_chain_file(ctx, "C:\\Users\\admin\\Desktop\\test\\test.crt") != 1) {  
  
        return 0;  
    }  
  
  
    if (SSL_CTX_use_PrivateKey_file(ctx, "C:\\Users\\admin\\Desktop\\test\\test.key", SSL_FILETYPE_PEM) != 1) {  
        return 0;  
    }  
    std::string const dhparams = "-----BEGIN DH PARAMETERS-----\n"  
                                 "MIIBCAKCAQEArzQc5mpm0Fs8yahDeySj31JZlwEphUdZ9StM2D8+Fo7TMduGtSi+\n"  
                                 "/HRWVwHcTFAgrxVdm+dl474mOUqqaz4MpzIb6+6OVfWHbQJmXPepZKyu4LgUPvY/\n"  
                                 "4q3/iDMjIS0fLOu/bLuObwU5ccZmDgfhmz1GanRlTQOiYRty3FiOATWZBRh6uv4u\n"  
                                 "tff4A9Bm3V9tLx9S6djq31w31Gl7OQhryodW28kc16t9TvO1BzcV3HjRPwpe701X\n"  
                                 "oEEZdnZWANkkpR/m/pfgdmGPU66S2sXMHgsliViQWpDCYeehrvFRHEdR9NV+XJfC\n"  
                                 "QMUk26jPTIVTLfXmmwU0u8vUkpR7LQKkwwIBAg==\n"  
                                 "-----END DH PARAMETERS-----\n";  
  
  
    DH* dh = PEM_read_bio_DHparams(BIO_new_mem_buf(dhparams.c_str(), dhparams.length()), NULL, NULL, NULL);  
    if (dh == NULL) {  
        return 0;  
    }  
```  
All SSL API calls are normal

---

## Comment 3

> Username: romanholidaypancakes  
> Created at: 2023-12-06 18:45:00 UTC  
> Updated at: 2023-12-06 18:46:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1843489588  

On a side note I tested it on my local machine, client and server are communicating on the same machine using 127.0.0.1

---

## Comment 4

> Username: ashtum  
> Created at: 2023-12-06 18:52:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1843506256  

In which handler are you getting the error, `on_read` ?

---

## Comment 5

> Username: romanholidaypancakes  
> Created at: 2023-12-06 18:54:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1843510658  

> In which handler are you getting the error, `on_read` ?  
  
yes   
```c++  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "read"); // Getting error here  
  
        // Close the WebSocket connection  
        ws_.async_close(websocket::close_code::normal,  
            beast::bind_front_handler(  
                &session::on_close,  
                shared_from_this()));  
    }  
```

---

## Comment 6

> Username: ashtum  
> Created at: 2023-12-06 19:24:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1843551613  

What is your platform and version of OpenSSL?

---

## Comment 7

> Username: romanholidaypancakes  
> Created at: 2023-12-07 12:51:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2774#issuecomment-1845288572  

when i restarted my computer and tried again everything was fine, i don't know what I'm missing, thanks!
