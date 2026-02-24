# #1226 - Is it possible to set up an IP for a websocket connection? [Closed]

> Username: litwr2  
> Created at: 2018-08-15 08:48:29 UTC  
> Updated at: 2018-09-21 16:40:29 UTC  
> Closed at: 2018-09-21 16:40:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1226  

I use the next code for this task.  
  
        boost::asio::io_context ioc;  
        ssl::context ctx{ssl::context::sslv23_client};  
        load_root_certificates(ctx);  
        tcp::resolver resolver{ioc};  
        websocket::stream<ssl::stream<tcp::socket>> ws{ioc, ctx};  
        auto const results = resolver.resolve(host, port);  
        /* start of the setting of IP address */  
        ws.next_layer().next_layer().open(boost::asio::ip::tcp::v4());  
        ws.next_layer().next_layer().bind(boost::asio::ip::tcp::endpoint(  
                        boost::asio::ip::address::from_string("51.52.10.5"), 0));  
        /* end of the setting of IP address */  
        boost::asio::connect(ws.next_layer().next_layer(), results.begin(), results.end());  
        ws.next_layer().handshake(ssl::stream_base::client);  
        ws.handshake(host, dir);  
  
It works but not the way required for me.  I want to use two websocket connections using two different IP addresses but the code when it is called for the second connection overwrites IP address set for the first connection.  Is there a way to use beast in the way required for me?  A lot of thanks in advance for any hint.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-15 13:41:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1226#issuecomment-413200786  

The issue is with Asio not Beast. You are trying to use the free function `boost::asio::connect` with a range of addresses. This will not work, because as you noted the binding is lost each time `connect` visits a new item in the range of results. Instead, you should simply write out the loop manually and call `ws.next_layer().next_layer().connect` yourself.

---

## Comment 2

> Username: litwr2  
> Created at: 2018-08-15 14:50:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1226#issuecomment-413222129  

Thanks! The next line does the trick.  
  
ws.next_layer().next_layer().connect(*results);

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-15 15:11:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1226#issuecomment-413228891  

That works too!

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-09-14 15:58:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1226#issuecomment-421404164  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-09-21 16:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1226#issuecomment-423598805  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
