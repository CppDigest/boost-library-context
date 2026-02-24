# #214 - WebSocket's async_read blocks instead of returning immediately [Closed]

> Username: ghost  
> Created at: 2016-12-24 14:32:43 UTC  
> Updated at: 2016-12-24 17:12:31 UTC  
> Closed at: 2016-12-24 17:12:31 UTC  
> Url: https://github.com/boostorg/beast/issues/214  

I am writing a simple C++ app processing data read from WebSocket (in this case Bitcoin order book of some Japanese exchange). Code and runes to compile below.  
  
What I am trying to achieve is to detect when network connectivity is lost, e.g. my laptop gets disconnected from Wi-Fi, or worse Wi-Fi network suddenly requires re-authentication and starts redirecting all the traffic to auth site. In that case, the `async_read` request just hangs forever. I've tried adding a `deadline_timer` but it seems that the thread is simply blocked on `ws.async_read` and having a timer is of no help. Am I misusing asio/Beast here or is this a bug?  
  
To simulate Wi-Fi going down, I am using `socat`:  
  
```  
socat TCP4-LISTEN:8888,fork,reuseaddr TCP4:ws.zaif.jp:8888  
```  
  
Then run the example:  
  
```  
beast_example localhost  
```  
  
Finally, I suspend the `socat` process (Ctrl-Z). The timeout never comes. I would like to be able to detect a situation where I have not received data from the WebSocket for a specified amount of time and display a warning to the user.  
  
```c++  
#include <iostream>  
#include <string>  
  
#include <beast/core/to_string.hpp>  
#include <beast/websocket.hpp>  
#include <beast/websocket/ssl.hpp>  
#include <boost/asio.hpp>  
#include <boost/asio/deadline_timer.hpp>  
#include <boost/asio/ssl.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
  
namespace asio = boost::asio;  
using stream_type = asio::ssl::stream<asio::ip::tcp::socket &>;  
  
void do_read(asio::deadline_timer &timer, beast::streambuf &sb, beast::websocket::opcode &op, beast::websocket::stream<stream_type &> &ws) {  
    timer.expires_from_now(boost::posix_time::seconds(5));  
    std::cout << "Reading from WebSocket\n";  
    ws.async_read(op, sb, [&](beast::error_code const &ec) {  
        if (ec) {  
            std::cout << "Read error: " << ec.message() << '\n';  
            return;  
        }  
  
        auto msg = beast::to_string(sb.data());  
        sb.consume(sb.size());  
        std::cout << "Received message " << msg << '\n';  
        do_read(timer, sb, op, ws);  
    });  
}  
  
int main(int argc, char *argv[]) {  
    asio::io_service ios;  
    asio::deadline_timer timer(ios);  
    namespace ssl = asio::ssl;  
    const char *host = (argc < 2) ? "ws.zaif.jp" : argv[1];  
    const char *port = "8888";  
    asio::ip::tcp::resolver resolver(ios);  
    asio::ip::tcp::socket sock(ios);  
    std::cout << "Connecting to " << host << ':' << port << '\n';  
    asio::connect(sock, resolver.resolve(asio::ip::tcp::resolver::query{host, port}));  
    ssl::context ctx{ssl::context::sslv23};  
    stream_type stream{sock, ctx};  
    stream.set_verify_mode(ssl::verify_none);  
    stream.handshake(ssl::stream_base::client);  
    beast::websocket::stream<stream_type &> ws{stream};  
    const char *resource = "/stream?currency_pair=btc_jpy";  
    std::cout << "Sending handshake\n";  
    ws.handshake(host, resource);  
    timer.async_wait([&](boost::system::error_code const &ec) {  
        if (ec == asio::error::operation_aborted)  
            return;  
  
        std::cout << "Closing WebSocket connection\n";  
        ws.close(beast::websocket::close_code::normal);  
    });  
    beast::streambuf sb;  
    beast::websocket::opcode op;  
    do_read(timer, sb, op, ws);  
    ios.run();  
    std::cout << "Closing WebSocket connection\n";  
    ws.close(beast::websocket::close_code::normal);  
    return 0;  
}  
```  
  
To compile on macOS, assuming OpenSSL from Homebrew:  
  
```bash  
g++ -std=c++14 -o bin/beast_example -I/Users/kwos/Projects/bitcoin-sor/build/external_projects/beast/src/include -I/usr/local/Cellar/openssl/1.0.2h_1/include -L/usr/local/Cellar/openssl/1.0.2h_1/lib -lcrypto -lssl -lboost_system src/beast_example.cpp  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-12-24 16:39:04 UTC  
> Updated at: 2016-12-24 16:39:25 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091213  

You need a `deadline_timer`. When the timer goes off, you should call `cancel` on the underlying socket (http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/basic_stream_socket/cancel.html).  
  
In your code you are calling `beast::websocket::stream<>::close`. There are two problems here. First, you are calling the synchronous version of `close`, which has no capability to time out (use `async_close` instead). Second, if you are in a situation where the remote end is no longer responding, it is not possible to gracefully close the websocket connection (which `close` tries to do). The only correct behavior is to cancel all I/O on the socket (by calling `cancel` as indicated earlier) and destroy the socket.  
  
The call to `cancel` would look like this:  
```  
    ws.next_layer().cancel();  
```  
  
Alternatively:  
```  
    beast::error_code ec;  
    ws.next_layer().cancel(ec);  
    ...  
```  
  
In other words:  
```  
    timer.async_wait([&](boost::system::error_code ec) {  
        if (ec == asio::error::operation_aborted)  
            return;  
  
        std::cout << "Closing WebSocket connection\n";  
        ec = {};  
        ws.next_layer().cancel(ec);  
    });  
```

---

## Comment 2

> Username: ghost  
> Created at: 2016-12-24 16:44:56 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091429  

@vinniefalco In the example above, I have a timer, which I reset every time I queue a read, but it never goes off. If I suspend the proxy the last thing I see on the terminal is  
```  
Reading from WebSocket  
```  
and it just hangs there forever. That's the behaviour that I find puzzling.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-12-24 16:53:41 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091731  

Your program works for me, this is some of the output:  
```  
...07915.0, "currency_pair": "btc_jpy", "tid": 22360217, "amount": 0.0152, "date": 1482598389}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360216, "amount": 0.0151, "date": 1482598387}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360215, "amount": 0.0151, "date": 1482598386}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360214, "amount": 0.0161, "date": 1482598385}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107925.0, "currency_pair": "btc_jpy", "tid": 22360213, "amount": 0.0142, "date": 1482598381}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360212, "amount": 0.0161, "date": 1482598376}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107890.0, "currency_pair": "btc_jpy", "tid": 22360211, "amount": 0.0242, "date": 1482598376}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107995.0, "currency_pair": "btc_jpy", "tid": 22360210, "amount": 0.0105, "date": 1482598372}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107995.0, "currency_pair": "btc_jpy", "tid": 22360209, "amount": 0.0185, "date": 1482598371}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107995.0, "currency_pair": "btc_jpy", "tid": 22360208, "amount": 0.0192, "date": 1482598371}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107960.0, "currency_pair": "btc_jpy", "tid": 22360207, "amount": 0.0001, "date": 1482598338}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107895.0, "currency_pair": "btc_jpy", "tid": 22360206, "amount": 0.0171, "date": 1482598332}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360205, "amount": 0.0004, "date": 1482598332}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107960.0, "currency_pair": "btc_jpy", "tid": 22360204, "amount": 0.0003, "date": 1482598329}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360203, "amount": 0.0005, "date": 1482598321}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360202, "amount": 0.0396, "date": 1482598321}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360201, "amount": 0.015, "date": 1482598319}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360200, "amount": 0.0001, "date": 1482598319}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107960.0, "currency_pair": "btc_jpy", "tid": 22360199, "amount": 0.0132, "date": 1482598316}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360198, "amount": 0.0241, "date": 1482598312}], "bids": [[107910.0, 0.0523], [107895.0, 0.0004], [107890.0, 0.1258], [107880.0, 0.1082], [107875.0, 0.0546], [107850.0, 0.0254], [107840.0, 0.0509], [107835.0, 0.3429], [107825.0, 1.0698], [107820.0, 0.3428], [107815.0, 0.3262], [107810.0, 0.1554], [107800.0, 0.4185], [107795.0, 0.0493], [107790.0, 0.049], [107780.0, 2.1355], [107770.0, 2.733], [107765.0, 0.7269], [107760.0, 0.14], [107735.0, 0.0159]], "currency_pair": "btc_jpy", "timestamp": "2016-12-25 01:53:10.602955"}  
Reading from WebSocket  
Received message {"asks": [[107925.0, 0.0007], [107965.0, 0.01], [107990.0, 0.0242], [107995.0, 0.0001], [108010.0, 0.0022], [108015.0, 5.6797], [108100.0, 1.007], [108160.0, 1.2275], [108170.0, 0.0994], [108175.0, 11.1012], [108180.0, 1.7516], [108230.0, 0.015], [108280.0, 0.02], [108320.0, 0.0143], [108375.0, 0.01], [108400.0, 0.4], [108415.0, 0.01], [108430.0, 0.03], [108500.0, 0.0938], [108555.0, 0.09]], "last_price": {"action": "ask", "price": 107910.0}, "target_users": ["a102ae95a9"], "trades": [{"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107910.0, "currency_pair": "btc_jpy", "tid": 22360218, "amount": 0.0028, "date": 1482598389}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360217, "amount": 0.0152, "date": 1482598389}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360216, "amount": 0.0151, "date": 1482598387}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360215, "amount": 0.0151, "date": 1482598386}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360214, "amount": 0.0161, "date": 1482598385}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107925.0, "currency_pair": "btc_jpy", "tid": 22360213, "amount": 0.0142, "date": 1482598381}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107915.0, "currency_pair": "btc_jpy", "tid": 22360212, "amount": 0.0161, "date": 1482598376}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price": 107890.0, "currency_pair": "btc_jpy", "tid": 22360211, "amount": 0.0242, "date": 1482598376}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107995.0, "currency_pair": "btc_jpy", "tid": 22360210, "amount": 0.0105, "date": 1482598372}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107995.0, "currency_pair": "btc_jpy", "tid": 22360209, "amount": 0.0185, "date": 1482598371}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107995.0, "currency_pair": "btc_jpy", "tid": 22360208, "amount": 0.0192, "date": 1482598371}, {"currenty_pair": "btc_jpy", "trade_type": "bid", "price": 107960.0, "currency_pair": "btc_jpy", "tid": 22360207, "amount": 0.0001, "date": 1482598338}, {"currenty_pair": "btc_jpy", "trade_type": "ask", "price":...  
```

---

## Comment 4

> Username: ghost  
> Created at: 2016-12-24 16:56:10 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091828  

Yes, but try bringing the network connection down, e.g. turn off Wi-Fi on your machine if that's how you connect or bring the interface down. Then it's going to hang. Another way I simulate this is by having a `socat` proxy which I then suspend.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-12-24 16:56:20 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091833  

The problem is with the way the deadline timer is being used. From:  
http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/basic_deadline_timer/expires_from_now/overload2.html  
  
>Any pending asynchronous wait operations will be cancelled.  
  
You need to call `expires_from_now` **before** calling `async_wait` on the timer.

---

## Comment 6

> Username: ghost  
> Created at: 2016-12-24 16:58:09 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091900  

@vinniefalco Ahh... Damn so it was just me being stupid!

---

## Comment 7

> Username: ghost  
> Created at: 2016-12-24 16:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091917  

This works as expected:  
```  
void do_read(asio::deadline_timer &timer, beast::streambuf &sb, beast::websocket::opcode &op, beast::websocket::stream<stream_type &> &ws) {  
    timer.expires_from_now(boost::posix_time::seconds(5));  
    timer.async_wait([&](boost::system::error_code const &ec) {  
        if (ec == asio::error::operation_aborted)  
            return;  
  
        std::cout << "Cancelling underlying socket\n";  
        ws.next_layer().next_layer().cancel();  
    });  
    std::cout << "Reading from WebSocket\n";  
    ws.async_read(op, sb, [&](beast::error_code const &ec) {  
        if (ec) {  
            std::cout << "Read error: " << ec.message() << '\n';  
            return;  
        }  
  
        auto msg = beast::to_string(sb.data());  
        sb.consume(sb.size());  
        std::cout << "Received message " << msg << '\n';  
        do_read(timer, sb, op, ws);  
    });  
}  
```

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-12-24 16:59:33 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091957  

Glad you got it working!

---

## Comment 9

> Username: ghost  
> Created at: 2016-12-24 17:00:06 UTC  
> Url: https://github.com/boostorg/beast/issues/214#issuecomment-269091979  

@vinniefalco Thanks so much for help :)
