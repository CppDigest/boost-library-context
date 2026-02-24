# #2627 - Websocket server not listening [Closed]

> Username: peb32  
> Created at: 2023-01-23 15:01:56 UTC  
> Updated at: 2023-01-23 18:03:04 UTC  
> Closed at: 2023-01-23 18:02:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2627  

Hello, I have been using boost beast for a bit now and I have finally prepared my application with it. For some reason it seems that boost beast is only working on local host and is not listening over the servers address. When I proceed to try to open one with another library like websocketpp, it works fine. Any help would be appreciated.  
  
Version: 345  
Compiler: MSVC 2017

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-01-23 15:06:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400502068  

I will need a bit more context.

---

## Comment 2

> Username: peb32  
> Created at: 2023-01-23 15:11:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400511307  

> I will need a bit more context.  
  
Apologies, I am working off this example here https://github.com/boostorg/beast/blob/develop/example/websocket/server/async/websocket_server_async.cpp  
  
Basically when I open the WebSocket server on 127.0.0.1 it works and nmap (which is what I am using to check if the port is open) sees that its open. When I throw it on a windows rdp and attempt to listen, nmap can not see that the port is open on the server ip.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-01-23 15:44:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400565115  

which port are you listening on? do you have another web server running or some firewall active?

---

## Comment 4

> Username: peb32  
> Created at: 2023-01-23 15:49:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400571492  

I am listening on port 8080 and no firewall active. I also have no other web server running on that port.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-01-23 16:00:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400588250  

SO 0.0.0.0 8080 ?   
Can you use it from the server when using the network facing IP address?

---

## Comment 6

> Username: peb32  
> Created at: 2023-01-23 16:03:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400592929  

Yes I am using 0.0.0.0 8080 and no I cannot use it.

---

## Comment 7

> Username: peb32  
> Created at: 2023-01-23 18:02:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400756602  

I have fixed the issue.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2023-01-23 18:03:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2627#issuecomment-1400757630  

Well don't keep us in suspense, what was the problem?
