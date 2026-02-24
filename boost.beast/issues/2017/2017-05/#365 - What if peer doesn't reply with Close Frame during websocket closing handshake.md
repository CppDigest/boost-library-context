# #365 - What if peer doesn't reply with Close Frame during websocket closing handshake? [Closed]

> Username: vchang-akamai  
> Created at: 2017-05-16 22:34:39 UTC  
> Updated at: 2017-05-19 06:36:18 UTC  
> Closed at: 2017-05-19 06:36:18 UTC  
> Url: https://github.com/boostorg/beast/issues/365  

To close a websocket connection, we call beast::websocket::async_close() (or close()) to send the Close Frame to the peer to initiate the closing handshake  Then we need to keep calling async_read() (or read()) until receiving error::closed which indicates that the peer has replied with its Close Frame.  
  
What if the peer is misbehaving (maybe DoS abuse) and never sends its Close Frame? Does Beast enforce any timeout limit for this?  Or we need to add our own?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-17 20:36:23 UTC  
> Url: https://github.com/boostorg/beast/issues/365#issuecomment-302224130  

You need to handle that timeout yourself

---

## Comment 2

> Username: vchang-akamai  
> Created at: 2017-05-19 06:36:18 UTC  
> Url: https://github.com/boostorg/beast/issues/365#issuecomment-302621455  

Ok, thanks for the info
