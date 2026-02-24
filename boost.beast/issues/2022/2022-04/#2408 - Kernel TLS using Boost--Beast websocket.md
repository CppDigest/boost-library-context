# #2408 - [Question] Kernel TLS using Boost::Beast websocket [Closed]

> Username: kim2lux  
> Created at: 2022-04-19 17:08:23 UTC  
> Updated at: 2022-04-26 20:51:09 UTC  
> Closed at: 2022-04-26 20:51:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2408  

Hello,  
  
Trying to play with the new Kernel TLS support in openssl 3 while using boost beast.  
I took the example from the openssl apps  **[s_client.c](https://github.com/openssl/openssl/blob/master/apps/s_client.c)**, I see that we should now use BIO_* to create the KTLS context (not using crypto_info directly as it is now internal).  
  
My problem is to use BIO in the boost beast context here.  
  
I am able to setup the SSL handshake and I see that the kernel TLS RX/TX is activated through the /proc/net/tls_stat (send and receive 1 packet each as in the SSL protocol).  
After the Websocket Handshake (reporting status is OK) I send an **AsyncWrite** on the websocket but the **io_context** does not get any update and is stuck here. I suspect I may have done something wrong with the underlying socket while trying to enable the KTLS. see **SetSSLBioSocket**.  
  
Did someone already setup the Kernel TLS using boost beast ? Any hint or help would be great. Here is the sample of the code, I may be missing something.  
  
Wireshark shows me that I am able to handshake and upgrade the socket to wss protocol, I can send a simple write but the async is not working.   
  
Thank you for your help.  
  
**BOOST_BEAST_VERSION: 322  
Boost: 1.78  
Openssl version: Library: OpenSSL 3.0.2  
TLS: 1.2**  
  
```  
using PtrBeastWS = std::unique_ptr<boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>>  
  
   bool SetSSLBioSocket(SSL* ssl, int socket) {  
      BIO * nBio = nullptr;  
      // new_socket calling setsockopt(fd, SOL_TCP, TCP_ULP, "tls", sizeof("tls")) ? 0 : 1;  
      nBio = BIO_new_socket(socket, BIO_NOCLOSE);  
      //BIO_set_nbio(nBio, 1);  
  
      if (nBio == nullptr)  
         return false;  
      SSL_set_bio(ssl, nBio, nBio);  
      return true;  
   }  
  
void connectWs(boost::asio::io_context &ioContext, boost::asio::ssl::context &sslContext, boost::asio::ip::tcp::resolver &resolver,  
                      const std::string &hostname, uint16_t port)   
{  
        PtrBeastWS webSocket(new PtrBeastWS(ioContext, sslContext));  
        auto &tcp_stream = boost::beast::get_lowest_layer(*webSocket);  
  
        SSL *ssl = webSocket->next_layer().native_handle();  
        SSL_set_cipher_list(ssl, "ECDHE-RSA-AES128-GCM-SHA256");  
        SSL_CTX *ctx = sslContext.native_handle();  
        SSL_CTX_set_options(ctx, SSL_OP_ENABLE_KTLS);  
  
        auto ep = tcp_stream.connect(results); //OK  
  
       // Set Kernel TLS socket T  
        if (!SetSSLBioSocket(ssl, tcp_stream.socket().native_handle())) {  
           return nullptr;  
        }  
  
        auto &ssl_stream = webSocket->next_layer();  
        ssl_stream.set_verify_mode(boost::asio::ssl::verify_none);  
        ssl_stream.handshake(boost::asio::ssl::stream_base::client); //OK  
        tcp_stream.expires_never();  
  
        webSocket->binary(binary);  
        webSocket->handshake(res, host, target, ec); // Response is OK  
  
         
       boost::asio::post( ..... webSocket.async_write(....)) //Handler write never called / nothing is sent looking at wireshark !  
}  
  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-04-19 17:13:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1102893035  

Looks like you are mixing synchronous and asynchronous calls... you can't do that :)

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-04-19 17:26:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1102904859  

I am not an expert on kernel TLS, but I've had a look at this page: https://www.kernel.org/doc/html/latest/networking/tls.html  
  
It seems to me that you can do away with the entire asio::ssl::stream altogether.  
  
Something like:  
  
1. negotiate the handshake using openssl  
2. get the kernel-TLS-enabled socket :  
`int tls_sock = extract_configured_socket(...);`  
  
declare a websocket stream using the TLS-enabled socket directly:  
  
`beast::websocket::stream<asio::ip::tcp::socket> ws { my_executor, tsl_sock };`  
  
And then use the websocket stream as normal.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-04-19 17:34:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1102912285  

Oh! Kernel TLS.. I get it now. That's pretty handy. Why can't Apple use this API instead of inventing their own thing. Anyway.. you still can't mix sync and async with Beast websocket.

---

## Comment 4

> Username: sehe  
> Created at: 2022-04-19 17:51:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1102926643  

This doesn't seem Beast specific. Maybe this question/idea could get more traction over at chriskohlhoff/asio (or boostorg/asio)? Chances are indeed that others have toyed with OpenSSL BIO* APIs, perhaps including KTLS

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-04-19 18:51:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1102976837  

It isn't Beast specific but we have kind of become the de-facto place to discuss Asio specific things as well, since the Asio upstream repository doesn't get much in terms of replies to issues.

---

## Comment 6

> Username: kim2lux  
> Created at: 2022-04-19 19:00:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1102984509  

Thank you for your help, I will update the async part and try to create a websocket from an opened SSL Socket (with ktls enabled through BIO).

---

## Comment 7

> Username: kim2lux  
> Created at: 2022-04-22 13:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2408#issuecomment-1106516799  

Hello,  
  
I was able to make it work through a raw tcp socket with the following type thanks to your advice:  
`beast::websocket::stream<asio::ip::tcp::socket> websocket`  
Thank you a lot for pointing this out. Basically I did all the SSL work using OpenSSL3, and retrieved the BIO Socket type that I assigned to my websocket: `ws.socket().assign(boost::asio::ip::tcp::v4(), sockTls);`  
  
Still something that I am missing here is that I don't understand why I could not use the previous type as in:  
`boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>> `  
I tried to create all the context through Openssl3 then assign it to the `SSL* `I retrieve through `ssl_stream.native_handle();` using `SSL_set_SSL_CTX`. Then assign the BIO socket the same way I was doing in the working example.  
  
But no way to make it work :(. Only difference I see is that the Async Read/Write is going through the `ssl_stream layer `and something is blocking when the context is made through an Openssl Context and assigned to the the `ssl_stream`.   
Maybe the ssl_stream is forcing a specific behavior that prevent the tls offload to work. If you have any idea why it could behave differently I would be really interested to know the difference here.  
  
I see this in the 1.79 release note openssl3 will be fully supported and we can create a ssl_stream from a SSL*: I will try it out using this version (currently on 1.78),  
  
- Added OpenSSL 3.0 compatibility.  
- Added support for adopting an existing SSL* into an ssl::stream<>.  
  
Thank you again for help/time here !  
  
Best,
