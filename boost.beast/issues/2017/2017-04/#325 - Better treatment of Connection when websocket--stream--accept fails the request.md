# #325 - Better treatment of Connection when websocket::stream::accept fails the request [Closed]

> Username: vinniefalco  
> Created at: 2017-04-27 00:48:48 UTC  
> Updated at: 2017-05-08 00:04:36 UTC  
> Closed at: 2017-05-08 00:04:36 UTC  
> Url: https://github.com/boostorg/beast/issues/325  

The `websocket::stream` implementation needs to better handle the case where the response to an incoming HTTP Upgrade request does not have Status = 101 (success). Specifically, decisions need to be made:  
  
* Whether or not to set Connection: close  
* If closing the connection, run teardown?  
* What diagnostics are provided to the caller? new error code?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-06 02:59:00 UTC  
> Url: https://github.com/boostorg/beast/issues/325#issuecomment-299611352  

We can simply deliver the `beast::websocket::error::handshake_failed` and leave it at that.
