# #2339 - The websocket handshake was declined by peer on read [Closed]

> Username: deepanshnagaria  
> Created at: 2021-11-08 14:50:57 UTC  
> Updated at: 2022-09-24 05:09:11 UTC  
> Closed at: 2022-09-24 05:09:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2339  

the piece of code below fails with an error  
  
```  
terminate called after throwing an instance of 'boost::wrapexcept<boost::system::system_error>'  
  what():  The WebSocket handshake was declined by the remote peer  
```  
The handshake works fine the first time around, the write works fine, but on read I get this error, any leads/help is highly appreciated. Thanks in advance:  
  
```  
namespace net       = boost::asio;  
namespace ssl       = net::ssl;  
namespace beast     = boost::beast;  
namespace http      = beast::http;  
namespace websocket = beast::websocket;  
using tcp = net::ip::tcp;  
using stream_t = websocket::stream<ssl::stream<tcp::socket>>;  
  
int test() {  
  
    std::string host = "vstream.binance.com";  
    auto const port = "443";  
    auto const path  = "/ws/BTC-211112-59000-P@depth20";  
    auto const rpcJson = "{\"method\":\"BINARY\", \"params\":[\"false\"], \"id\":1}";  
  
    net::io_context ioc;  
    tcp::resolver resolver{ ioc };  
  
    ssl::context ctx { ssl::context::sslv23 };  
    ctx.set_verify_mode(ssl::verify_none);  
  
    stream_t s{ ioc, ctx };  
    net::connect(beast::get_lowest_layer(s), resolver.resolve(host, port));  
  
    // SSL handshake  
    s.next_layer().handshake(ssl::stream_base::client);  
  
    s.handshake(host + ":" + port, path);  
  
    std::cout << "connected." << std::endl;  
  
    // send request to the websocket  
    s.write(net::buffer("{\"method\":\"BINARY\", \"params\":[\"false\"], \"id\":1}"));  
  
    {  
        boost::beast::multi_buffer m_buf;  
        s.read(m_buf);  
        auto size = m_buf.size();  
        std::string strbuf;  
        strbuf.reserve(size);  
  
        for ( const auto &it: m_buf.data() ) {  
            strbuf.append(static_cast<const char *>(it.data()), it.size());  
        }  
        m_buf.consume(m_buf.size());  
  
        std::cout << "s. " << strbuf << std::endl;  
    }  
    return 0;  
}  
```  
The major confusion I have is why didn't the initial handshake fail and what caused it to fail on read.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-11-08 17:09:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-963374784  

I've just compiled this program and run it, and it worked.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-11-08 17:34:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-963396804  

I created this repo to replicate:  
https://github.com/beast-issues/2339  
  
Output:  
```  
connected.  
s. {"id":1}  
```  
  
With exit code 0.  
  
Please confirm you get the same results with this program.

---

## Comment 3

> Username: deepanshnagaria  
> Created at: 2021-11-08 17:55:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-963413298  

Hi @madmongo1 actually no, as I mentioned, I get the above-mentioned error, the link you mentioned doesn't work for me, do let me know if you used the exact code and if yes, what versions of libraries did you use?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-11-08 18:14:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-963431199  

My apologies. For some reason I had mistakenly set the repo to "private".  
  
Please try again.  
  
https://github.com/beast-issues/2339  
  
I copy/pasted your code and then modified the final printing step to ensure there were no unprintable characters printed to the console.

---

## Comment 5

> Username: deepanshnagaria  
> Created at: 2021-11-10 05:36:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-964804066  

Hi, I am not sure if this is something that is wrong at my end or otherwise, but I get the same error again and again at this ednpoint. I don't get the output you mentioned.

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-11-10 06:33:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-964827492  

I think the first thing to check is basic connectivity. Is it worth augmenting the SSL stream by setting the SNI?  
  
https://github.com/boostorg/beast/blob/bd5e702c9004f2fff828735676c260972f1cd37d/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp#L74  
  
Just in case the instance you're talking to is behind a TLS proxy?  
  
The other question to ask is whether you're behind a company firewall.  
  
What happens if you connect with another websocket tool, such as wscat?  
  
https://www.npmjs.com/package/wscat

---

## Comment 7

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-1008220701  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:01:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2339#issuecomment-1256861052  

@deepanshnagaria Is this issue resolved?
