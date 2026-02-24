# #949 - Verbose websocket error codes and messages [Closed]

> Username: vinniefalco  
> Created at: 2017-12-24 03:40:07 UTC  
> Updated at: 2018-01-12 20:28:48 UTC  
> Closed at: 2018-01-12 20:28:48 UTC  
> Url: https://github.com/boostorg/beast/issues/949  

When a websocket stream operating in client mode receives an invalid or disallowed WebSocket upgrade response, there should be very fine grained error codes and descriptive error messages. For example  
  
```  
error::invalid_http_version  
"the websocket upgrade response contains an invalid HTTP version"  
  
error::missing_accept_field  
"the websocket upgrade response is missing the Sec-Websocket-Accept field"  
```  
  
We can introduce an `error_condition` code to group all of these upgrade failures together for equivalence.
