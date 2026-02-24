# #314 - decorator/handshake documentation [Closed]

> Username: vinniefalco  
> Created at: 2017-04-22 00:29:46 UTC  
> Updated at: 2017-05-08 00:03:59 UTC  
> Closed at: 2017-05-08 00:03:59 UTC  
> Url: https://github.com/boostorg/beast/issues/314  

The websocket **decorator** option needs explicit documentation on how to inspect and modify HTTP headers, since the question comes up often. There should be examples. Such as setting the "Origin" field for client upgrade requests. Another example should show how to inspect and set subprotocols.  
  
The doc could also use a section explaining in detail how to inspect or customize the HTTP Upgrade handshakes for each case (client/server, request/response).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-04-24 17:20:47 UTC  
> Url: https://github.com/boostorg/beast/issues/314#issuecomment-296755052  

See #317
