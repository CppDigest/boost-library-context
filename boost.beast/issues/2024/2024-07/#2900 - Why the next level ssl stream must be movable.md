# #2900 - Why the next level ssl stream must be movable? [Closed]

> Username: Mavis-Dong  
> Created at: 2024-07-09 15:10:22 UTC  
> Updated at: 2024-07-11 15:50:54 UTC  
> Closed at: 2024-07-11 15:49:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2900  

Hello! I met a ssl handshake error when using s2n as the next level ssl stream.  
The return value of s2n_negotiate is -1 and the error message is "connection is closed". I suspect it's because I didn't add explicit move constructor to my customized s2n ssl stream, and its default move constructor didn't move socket correctly to beast::web socket::stream.  
I see the example is passing the right value of ssl stream: https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp#L400  
Could you help explain the reason? Any constrains from asio lib? Thank you!

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-09 17:46:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2900#issuecomment-2218310701  

It's difficult to determine the issue without seeing the code. However, if you want to quickly test whether the problem is with the move constructor, you can remove it and try constructing `beast::websocket::stream` by passing the necessary arguments for constructing the mentioned next layer type. (instead of moving that socket type into `beast::websocket::stream`)

---

## Comment 2

> Username: Mavis-Dong  
> Created at: 2024-07-11 15:50:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2900#issuecomment-2223298211  

I've verified the websocket::stream is corrected constructed with a right value of ssl stream parameter, you may close this issue, thanks.
