# #354 - Using SSL with Beast, how to make sure SSL connection is fully shutdown and released [Closed]

> Username: generationtech  
> Created at: 2017-05-11 01:52:00 UTC  
> Updated at: 2017-08-04 23:10:24 UTC  
> Closed at: 2017-08-04 23:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/354  

I've started using Beast with SSL. My previous non-SSL use of Beast worked just fine. Now with SSL, I want to make sure my client-side websocket is completely closing, shutting down, and releasing the C++ SSL interface. I'm having a problem unloading a DLL and it seems maybe I'm leaving the SSL in some incomplete state which is keeping the *.dll file locked. This doesn't happen for me when I use  my original non-SSL implementation.  
  
I'm running a websocket manager class.  
  
Header:  
`    std::string                     wsm_SERVER_URL;`  
`    boost::asio::io_service         wsm_ios;`  
`    boost::asio::io_service::work   wsm_work{ wsm_ios };`  
`    boost::asio::ip::tcp::resolver  wsm_r{ wsm_ios };`  
`    boost::asio::ip::tcp::socket    wsm_sock{ wsm_ios };`  
`    std::unique_ptr<beast::websocket::stream< boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>> wsm_client;`  
`    boost::asio::ssl::context wsm_ctx{ boost::asio::ssl::context::sslv23 };`  
  
Initialize method:  
`wsm_client = std::make_unique<beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>>(wsm_ios, wsm_ctx);`  
  
Typical use:  
`    boost::asio::ip::tcp::resolver::iterator iter = wsm_r.resolve(boost::asio::ip::tcp::resolver::query{ wsm_SERVER_URL, "https" });`  
`    const boost::asio::ip::tcp::endpoint EP = iter->endpoint();`  
`    boost::system::error_code EC;`  
`    wsm_client->next_layer().next_layer().connect(EP, EC);`  
`    wsm_client->next_layer().set_verify_mode(boost::asio::ssl::verify_none);`  
`    wsm_client->next_layer().handshake(boost::asio::ssl::stream_base::client);`  
`    wsm_client->handshake(wsm_SERVER_URL, srv_url);`  
  
Deactivate:  
`wsm_client->next_layer().next_layer().close();`  
`wsm_client->next_layer().next_layer().cancel();`  
`wsm_client->close(beast::websocket::close_code::normal);`  
`wsm_client.reset();`  
`wsm_ios.stop();`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-11 13:01:39 UTC  
> Url: https://github.com/boostorg/beast/issues/354#issuecomment-300781948  

Could this be relevant?  
http://openssl.6102.n7.nabble.com/Openssl-static-build-linked-in-DLL-does-not-unload-on-win32-td69507.html

---

## Comment 2

> Username: generationtech  
> Created at: 2017-05-11 19:18:27 UTC  
> Url: https://github.com/boostorg/beast/issues/354#issuecomment-300890286  

Hmm, I am statically linking openssl from this library   
https://slproweb.com/products/Win32OpenSSL.html  
  
I suppose I'll check into the possibility of dynamically including the library at runtime.  
  
Thanks for the link, that was very helpful

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-05-22 14:25:36 UTC  
> Url: https://github.com/boostorg/beast/issues/354#issuecomment-303115661  

Is this still an issue?

---

## Comment 4

> Username: generationtech  
> Created at: 2017-05-26 13:19:36 UTC  
> Url: https://github.com/boostorg/beast/issues/354#issuecomment-304280494  

I found that I was already dynamically linking the openssl library, which seems to be why I can't fully unload and release the file-level lock on my own dll. From all I can gather, this seems to be normal behavior for openssl when dynamically linked. Even though all websockets are properly terminated and the SSL variables closed or reset, openssl is going to keep some kind of connection open to the other dll.  
  
Next, when I have some more development time, I'm going to try to statically-link openssl into my dll. I know this is going to bloat my dll, but I really need the ability to unload/replace/reload the dll while the main C# application is running. And I'd really like to have my websockets protected by SSL. For all my purposes, non-SSL is working great.  
  
I suppose I'll continue to update this ticket as I know more so anyone else facing this issue will have more information.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-04 02:05:48 UTC  
> Url: https://github.com/boostorg/beast/issues/354#issuecomment-312763111  

Any progress on this?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-08-04 23:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/354#issuecomment-320372109  

It looks like there is nothing actionable here for me so I'm going to go ahead and close the issue. If you are still having some trouble please re-open the issue or even better create a new issue. Thanks!
