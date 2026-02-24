# #1144 - the demo “websocket-client-async-ssl” to get data from  "wss://stream.binance.com:9443/ws/bnbeth@depth" [Closed]

> Username: weiweiloong  
> Created at: 2018-05-29 02:53:29 UTC  
> Updated at: 2020-06-07 20:28:29 UTC  
> Closed at: 2018-06-07 02:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1144  

hello;  
        I learn to use “websocket-client-async-ssl” to get data from  "wss://stream.binance.com:9443/ws/bnbeth@depth"  
  
I get the correct data using other websocket client tool but I get nothing form webserver as my code like this:  
  
	auto const host = "wss://stream.binance.com";//  
	auto const port = "9443";  
	auto const text = "/ws/bnbeth@depth";  
    // The io_context is required for all I/O  
    boost::asio::io_context ioc;  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::sslv23_client};  
    // This holds the root certificate used for verification  
    load_root_certificates(ctx);  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc, ctx)->run(host, port, text);  
  
    // Run the I/O service. The call will return when  
    // the socket is closed.  
    ioc.run();

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-29 03:07:53 UTC  
> Updated at: 2018-05-29 03:19:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-392640508  

`host` should be `"stream.binance.com"`  
  
Also, you need to use your own certificate routines, as the code from the examples is not really production quality.

---

## Comment 2

> Username: weiweiloong  
> Created at: 2018-05-29 03:52:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-392645475  

I did it as your way ,but it output "handshake: The WebSocket handshake was declined by the remote peer";  
ws_.async_handshake is faild;  
i debug the code find it run this pos..  
void on_handshake(boost::system::error_code ec)  
{  
if(ec)  
return fail(ec, "handshake");  
  
    // Send the message  
    ws_.async_write( boost::asio::buffer(text_), std::bind( &session::on_write, shared_from_this(), std::placeholders::_1, std::placeholders::_2));  
}

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-05-29 16:54:44 UTC  
> Updated at: 2018-05-29 16:55:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-392851559  

What is the exact code you are using, and what is the exact HTTP response?

---

## Comment 4

> Username: weiweiloong  
> Created at: 2018-06-06 02:56:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-394924165  

hello , i refer to the example: beast\example\websocket\client\sync-ssl  
this is my request  
I use websocket request "wss://stream.binance.com:9443/ws/bnbbtc@depth"  
add certificate or not add a certificate always error : WebSocket upgrade handshake failed  
code :  
  
		auto host = "stream.binance.com";  
		auto port = "9443";  
		auto text = "ws/bnbbtc@depth";  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
        // The SSL context is required, and holds certificates  
        ssl::context ctx{ssl::context::sslv23_client};  
		std::string filename = "./cacert.pem";  
		ctx.set_verify_mode(ssl::verify_none);  
		//ctx.load_verify_file(filename);  
		  
        tcp::resolver resolver{ioc};  
        websocket::stream<ssl::stream<tcp::socket>> ws{ioc, ctx};  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::connect(ws.next_layer().next_layer(), results.begin(), results.end());  
  
how could i do ?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-06-06 11:18:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-395033437  

You did not post all the code, where is the call to `ws.handshake(...)`?

---

## Comment 6

> Username: weiweiloong  
> Created at: 2018-06-07 02:23:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-395271907  

this issue Has been resolved. tks

---

## Comment 7

> Username: weiweiloong  
> Created at: 2018-06-07 02:44:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-395275033  

:)

---

## Comment 8

> Username: MailboxMember  
> Created at: 2018-08-23 13:23:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-415412221  

I have the same problem... What was the solution, @weiweiloong?

---

## Comment 9

> Username: Rohan0993  
> Created at: 2018-12-19 13:12:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-448592331  

@weiweiloong You should post your work around to this problem here or some leads for the people who read your post. Could you please post the answer?

---

## Comment 10

> Username: sergey-at-ark  
> Created at: 2019-11-18 01:40:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-554815702  

One of the reasons when "The WebSocket handshake was declined by the remote peer" could happen is when request target in async_handshake() is not setup correctly. In Vinnie's demo request target is hard-coded to "/". For the this case it should be "/ws" or something more elaborated depending on the rules set out by stream.binance.com.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-11-18 01:48:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-554816889  

funds are safu

---

## Comment 12

> Username: sergey-at-ark  
> Created at: 2019-11-18 14:04:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-555028518  

I don't know what "funds are safu" means :-)

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-11-18 14:07:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-555029591  

Oh seriously? You're connecting to binance.com and you never heard of "funds are safu?" Check out this video: https://www.youtube.com/watch?v=DelF6zEHXpE

---

## Comment 14

> Username: sergey-at-ark  
> Created at: 2019-11-18 23:46:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-555263700  

Cool. Thank you for the link. No, I never used binance.com... It's just I had the same issue with other exchange.

---

## Comment 15

> Username: t0mpl  
> Created at: 2020-06-07 20:28:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1144#issuecomment-640275047  

for the people who might come by like me, the problem is (as sergey said) the "/" when you do your handshake, binance doesn't seem to like it. You should add an endpoint here, example: ws_.async_handshake(host_, "/ws/bnbbtc@depth",  
                            beast::bind_front_handler(  
                                &session::on_handshake,  
                                shared_from_this()));
