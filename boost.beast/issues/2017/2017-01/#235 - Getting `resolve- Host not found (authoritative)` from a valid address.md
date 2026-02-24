# #235 - Getting `resolve: Host not found (authoritative)` from a valid address. [Closed]

> Username: UndarkAido  
> Created at: 2017-01-22 18:25:51 UTC  
> Updated at: 2017-01-22 23:52:39 UTC  
> Closed at: 2017-01-22 23:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/235  

I'm trying to convert [my discord library, Discord++,](https://github.com/Aidoboy/discordpp) from Websocket++ to Beast, as Webscoket++ was (I think) causing random disconnects, and I'm getting a `resolve: Host not found (authoritative)` at the `boost::asio::connect` in   
```  
std::string const host = fetchGateway(token);  
    boost::asio::ip::tcp::resolver r{asio_ios_};  
    boost::asio::ip::tcp::socket sock{asio_ios_};  
    boost::asio::connect(sock,  
                         r.resolve(boost::asio::ip::tcp::resolver::query{host, "80"}));  
```  
as well as the SSL example. `fetchGateway(token)` is calling [this Discord API endpoint](https://discordapp.com/developers/docs/topics/gateway#get-gateway) to get the URL `wss://gateway.discord.gg` with CurlPP. It worked with Websocket++, I'm probably doing something very wrong here. Does anyone know what?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-01-22 20:49:51 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274358301  

Hmm....your code looks right. I'm not sure what the problem is, or even if there is a problem. It looks like your program (or computer) is not connected to the Internet, or DNS services are not configured correctly. This StackOverflow question seems to address your use case, maybe it can help?  
http://stackoverflow.com/questions/5971242/how-does-boost-asios-hostname-resolution-work-on-linux-is-it-possible-to-use-n

---

## Comment 2

> Username: UndarkAido  
> Created at: 2017-01-22 21:08:49 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274359562  

I'll give to SO question you linked a read. The part that's really confusing me is that the old Websocket++ version is still making the connection, but this can't.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-01-22 21:16:03 UTC  
> Updated at: 2017-01-22 21:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274360166  

If possible, try to step through websocket++ and see what the API calls are returning, and compare that with what you're getting from your own code. What's the value of `host` in your program? Is it "gateway.discord.gg" Or is it "wss://gateway.discord.gg"?

---

## Comment 4

> Username: maddinat0r  
> Created at: 2017-01-22 21:16:31 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274360216  

You have to remove the protocol in the gateway URL and use `https` instead of the port. You can take a look at [how I did this here](https://github.com/maddinat0r/samp-discord-connector/blob/master/src/CNetwork.cpp#L96).  
I also get regular disconnects from the Discord gateway, and I suspect Discord being at fault here rather than the client library.

---

## Comment 5

> Username: nbougalis  
> Created at: 2017-01-22 21:16:51 UTC  
> Updated at: 2017-01-22 21:19:18 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274360254  

What is the value of `host` in your example? You seem to suggest that it is: `wss://gateway.discord.gg` so two comments:  
  
1. if `host` includes the `wss://` then you should strip it out and pass only `gateway.discord.gg`.  
2. if you want `wss` then the port should be 443, not 80.

---

## Comment 6

> Username: UndarkAido  
> Created at: 2017-01-22 21:30:55 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274361271  

Thank you everyone! New error, so that's progress, I guess. I now have:  
```cpp  
// Normal boost::asio setup  
    std::string host = fetchGateway(token);  
    std::string::size_type protocolPos = host.find("wss://");  
    if (protocolPos != std::string::npos) {  
        host.erase(protocolPos, 6);  
    }  
    boost::asio::ip::tcp::resolver r{asio_ios_};  
    boost::asio::ip::tcp::socket sock{asio_ios_};  
    boost::asio::connect(sock,  
                         r.resolve(boost::asio::ip::tcp::resolver::query{host, "https"}));  
```  
Which results in  
```  
terminate called after throwing an instance of 'boost::system::system_error'  
  what():  upgrade request failed  
```  
Anyone know what this means?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-01-22 21:32:52 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274361391  

`upgrade request failed` is a Beast error. It looks like you're connecting to their `https` protocol. Try using `query{host, "wss")`  
  
Also, where's the rest of your code? The part that sets up the `beast::websocket::stream`? You're trying to do OpenSSL are you handshaking correctly? Where's the code?

---

## Comment 8

> Username: UndarkAido  
> Created at: 2017-01-22 21:34:07 UTC  
> Updated at: 2017-01-22 21:35:06 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274361478  

I added some debug prints, it's actually failing a few lines later, at the handshake. Give me a sec to try some stuff.  
Edit: `query{host, "wss")` results in `resolve: Service not found`

---

## Comment 9

> Username: UndarkAido  
> Created at: 2017-01-22 21:41:03 UTC  
> Updated at: 2017-01-22 21:43:38 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274361922  

I tried `"80",` `"http"`, and `"wss"`; none of them seemed to work.  
  
[Here's the code in question](https://github.com/Aidoboy/discordpp/blob/beast/src/client.cc#L79)  
  
Edit: The failure is coming at line 88, from `ws.handshake(host, "/");`.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-01-22 21:45:01 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274362182  

If you want to connect to Secure WebSockets ("wss") you need to use OpenSSL. See:  
https://github.com/vinniefalco/Beast/blob/master/examples/ssl/websocket_ssl_example.cpp

---

## Comment 11

> Username: nbougalis  
> Created at: 2017-01-22 21:46:30 UTC  
> Updated at: 2017-01-22 21:47:01 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274362271  

You seem to be using HTTP but trying to talk to an HTTPS server. You will need to use the SSL-aware Boost ASIO sockets.

---

## Comment 12

> Username: UndarkAido  
> Created at: 2017-01-22 21:55:47 UTC  
> Updated at: 2017-01-22 21:56:52 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274362905  

Ok, I switched back to the SSL code I'd been trying to use with the wss::// clipped off, and it gets through the connections. Now I'm getting `Process finished with exit code 139 (interrupted by signal 11: SIGSEGV)` which I think is coming from sometime after I tell `asio_ios_` to run. Did I mess up something with my async?  
  
Edit: Pushed, so my rep reflects what I'm talking about here.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-01-22 22:01:59 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274363340  

Sounds like you have a lot going on there. Consider taking the Beast websocket SSL example as a starting point, change the code to connect to the Discord server, and then slowly adapt the code to suit your needs - this way, you start from something that works.

---

## Comment 14

> Username: UndarkAido  
> Created at: 2017-01-22 22:03:30 UTC  
> Updated at: 2017-01-22 22:04:13 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274363472  

Ok  
  
Edit: I have homework to do and I haven't eaten today, but I'll get back to it as soon as I can. Thank you so much everyone!

---

## Comment 15

> Username: UndarkAido  
> Created at: 2017-01-22 23:52:39 UTC  
> Url: https://github.com/boostorg/beast/issues/235#issuecomment-274370775  

Closing this, since the issue it's about was solved.
