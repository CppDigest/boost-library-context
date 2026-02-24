# #2748 - Is it possible to use two simultaneous certificates? [Closed]

> Username: Bugzero71  
> Created at: 2023-10-11 10:38:20 UTC  
> Updated at: 2024-01-07 07:48:53 UTC  
> Closed at: 2024-01-07 07:48:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2748  

Hi Vinnie,  
  
Thanks for this superb library.  
  
I am implementing a web socket to interface with another application.  
  
I am instantiating an asynchronous server and client using boost::beast::http library, and it should secure the connection with TLS 1.3. My client asks me to use a certificate for the client and one for the server, but also the possibility of loading a second certificate at a time when the first one is about to expire, both coexisting during the transition time. The idea is not to close the interface processes during the migration.  
  
Now I'm using the same boost::asio::io_context for both server and client, but different boost::asio::ssl::context for both.  
  
Do you know if it is possible to load different certificates in a boost::asio::ssl::context?  
Do you think in that case it should be better to replace the certificate and restart its instance if no client is connected and let the client accept the new one?  
  
I think it is a difficult architecture, but maybe someone else needs this in a project.  
  
Thanks in advance

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-10-22 18:15:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2748#issuecomment-1774162346  

Note that this is strictly an Asio question. I don't mind it being asked here however. The use of certificates is poorly documented and not many people know the answers.

---

## Comment 2

> Username: ashtum  
> Created at: 2023-10-29 17:18:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2748#issuecomment-1784173749  

@Bugzero71, can't you simply replace the new certificate within the context when it's time? The already established TLS streams should continue to work without any issues.
