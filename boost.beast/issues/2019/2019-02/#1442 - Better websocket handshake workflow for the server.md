# #1442 - Better websocket handshake workflow for the server [Open]

> Username: vinniefalco  
> Created at: 2019-02-09 01:09:39 UTC  
> Updated at: 2025-08-22 00:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1442  

Currently servers that want full control over accepting a websocket upgrade have to:  
  
1. read the upgrade request themselves using `http::read`  
2. use `websocket::is_upgrade` to determine if the message contains a valid websocket upgrade  
3. decide if they want to attempt the upgrade  
  - if not, composed an HTTP error response and send it  
  - if yes:  
4. call `websocket::stream::accept` with the request  
5. optionally provide a decorator to modify the 101 switching protocols response before it is sent  
  
In this workflow there is no way for the server to determine first if the upgrade would fail at the stream level. For example by requesting invalid permessage-deflate settings. There is no way to identify defective upgrade requests and send back a rationale 400 Bad Request.  
  
It should be possible to present the HTTP request to the stream to preflight it for validity, and if invalid then fill out a suitable default 400 Bad Request response which the caller can further customize before sending.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-08-22 00:42:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1442#issuecomment-3212581815  

@ashtum we need to make sure this works in ws-proto
