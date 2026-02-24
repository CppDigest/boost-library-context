# #1609 - Port sharing on Windows using websockets [Closed]

> Username: blayet  
> Created at: 2019-05-13 12:00:42 UTC  
> Updated at: 2019-07-20 14:02:50 UTC  
> Closed at: 2019-07-20 14:02:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1609  

Is it possible to configure beast and asio to allow port sharing with an independent HTTP library (to which I have source)?  
I would like to use beast to add websocket support to an existing application.  
Any pointers appreciated.  
many thanks  
ben

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-05-13 15:54:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1609#issuecomment-491880928  

What is port sharing?

---

## Comment 2

> Username: blayet  
> Created at: 2019-05-14 07:22:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1609#issuecomment-492111024  

From https://docs.microsoft.com/en-us/dotnet/framework/wcf/feature-details/net-tcp-port-sharing:  
"The HTTP.SYS model in which traffic for many different HTTP applications is multiplexed onto a single TCP port has become standard on the Windows platform." etc  
So in my case I want my web app to make http and websocket connections (latter using beast) to port 80. I know that a websocket connection can be made by upgrading an http connection so maybe this can work with beast? But I see in the simple websocket server example that a tcp::acceptor is configured with an ip address and port. In this case the "port sharing" I want doesn't happen. Either the http library or beast gets sole access to the port.  
We could upgrade our http library to handle the http upgrade to websockets and handle the ws protocol  itself - but I was hoping for an easier route by using beast  (I'm far from being an expert in this area...)  
thanks  
ben

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-06-13 08:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1609#issuecomment-501597871  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-06-13 12:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1609#issuecomment-501691076  

Beast doesn't handle ports or listening sockets, that's up to Asio. So I would suggest you look to Asio to answer your question.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-07-13 13:22:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1609#issuecomment-511121820  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-07-20 14:02:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1609#issuecomment-513470165  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
