# #169 - Where to reinstate SSL verification spec? [Closed]

> Username: grapland0  
> Created at: 2023-11-29 00:35:30 UTC  
> Updated at: 2025-09-08 20:34:47 UTC  
> Closed at: 2025-09-08 20:34:47 UTC  
> Url: https://github.com/boostorg/redis/issues/169  

in `example/cpp20_intro_tls.cpp`, it does  
  
```  
conn->next_layer().set_verify_mode(asio::ssl::verify_peer);  
conn->next_layer().set_verify_callback(verify_certificate);  
```  
  
before `async_exec`.  
  
I wonder whether this survives auto-reconnect? If not, where should I reinstate these for new connections?

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-12-01 07:57:40 UTC  
> Url: https://github.com/boostorg/redis/issues/169#issuecomment-1835639675  

Reconnection is unfinished for ssl. I am think about adding a callback that will be called after [this](https://github.com/boostorg/redis/blob/7caea928affde85146b738eb456d4772a410f721/include/boost/redis/connection.hpp#L55). Until there you can disable reconnection by setting the [this](https://github.com/boostorg/redis/blob/7caea928affde85146b738eb456d4772a410f721/include/boost/redis/config.hpp#L80) to zero writing yourself the loop from the link above, it is easier if you are using coroutines.

---

## Comment 2

> Username: grapland0  
> Created at: 2023-12-01 09:28:51 UTC  
> Updated at: 2023-12-01 10:39:33 UTC  
> Url: https://github.com/boostorg/redis/issues/169#issuecomment-1835759404  

Before the reconnection ssl support is completed, can we let it throw an exception if users enable both reconnection and SSL?  
  
Current implementation may introduce a vulnerability as:  
  
step 1, the MITM forces the client to drop a connection.  
step 2, MITM starts to intercept all connection from client to server, with a self-signed certificate.  
step 3, boost.redis attempts to reconnect with refreshed default ssl modes, which in some implementation, to be `ssl::verify_none` equivalently.  
step 4, connection won't fail by the forged certificate. then the MITM can monitor the traffic on this connection.

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-01-04 10:00:57 UTC  
> Url: https://github.com/boostorg/redis/issues/169#issuecomment-1876819541  

I have opened a PR with the fix. Please have a look.

---

## Comment 4

> Username: mzimbres  
> Created at: 2024-01-04 13:23:15 UTC  
> Url: https://github.com/boostorg/redis/issues/169#issuecomment-1877089253  

@anarthal pointed out that if you set your options on the `ssl::context`, all subsequent connections created from it will use the new configuration. That would make my PR above unnecessary. @grapland0 Do you have a use case where setting options in the context directly is not possible?

---

## Comment 5

> Username: anarthal  
> Created at: 2025-09-08 20:34:47 UTC  
> Url: https://github.com/boostorg/redis/issues/169#issuecomment-3267909934  

This is fixed as of today so I'll be closing this ticket.
