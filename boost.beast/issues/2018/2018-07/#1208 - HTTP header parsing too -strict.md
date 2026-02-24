# #1208 - HTTP header parsing too "strict"? [Closed]

> Username: HugoGuiroux  
> Created at: 2018-07-24 08:41:12 UTC  
> Updated at: 2018-07-27 15:33:24 UTC  
> Closed at: 2018-07-27 12:48:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1208  

### Version of Beast  
  
167  
  
### Problem  
  
Some websites send an invalid HTTP header (in my case, a `Set-Cookie` header), which leads to request rejection by the parser.  
  
For example, the response of a GET request to `https://www.cryptopia.co.nz/signalr/negotiate` (via HTTP/1.1) sends two wrongly-formatted cookies (according to the [RFC](https://tools.ietf.org/html/rfc6265#section-4.1.1)): one having a `\01` byte inside the cookie value and one with two `\r\n` bytes inside the cookie value.  
  
The culprit is the `basic_parser::parse_token_to_eol` function that returns an error while parsing the `\01` byte. (I don't know how it would parse the second cookie, if it returns an error or simply ignore the cookie with `\r\n`.)  
  
While the problem comes from either the website or their CDN (Incapsula), I think there should be a way to deal with such a situation. Curl, WGet and web-browsers seem to ignore the `\01` byte and treat the cookie with `\r\n` as 2 different headers.  
  
Is there a way to make the parser less "strict" or to hook a user-defined function before parsing the header to rewrite the faulty header?  
  
### Steps necessary to reproduce the problem  
  
`curl  --http1.1 -D /tmp/header -vvv "https://www.cryptopia.co.nz/signalr/negotiate"`  
  
Then open `/tmp/header` with an hex editor and look for cookies `___utmva` and `___utmvb`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-24 12:46:45 UTC  
> Updated at: 2018-07-24 12:47:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1208#issuecomment-407394095  

> Is there a way to make the parser less "strict" or to hook a user-defined function before parsing the header to rewrite the faulty header?  
  
Kind of. You can filter the incoming data to replace the `\01` and spurious `\r\n` with spaces. Here's an example of a stream filter:  
https://github.com/boostorg/beast/blob/e23ecc8ac903b303b9d1a9824b97c092cb3c09bd/include/boost/beast/experimental/http/icy_stream.hpp#L23  
  
I would be more than happy to answer questions about this code and help you get your filter working.

---

## Comment 2

> Username: HugoGuiroux  
> Created at: 2018-07-25 09:12:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1208#issuecomment-407688744  

I found a bit overkill to define a full new `Stream` just to filter three bytes.  
I was thinking of defining my own `http::parser` deriving from the `http::response_parser` and override the `put` method to filter there, but it fails at calling my method (I guess because `put` is not virtual, but not 100% sure).  
  
So far here what I did (for my very specific case, I use a `dynamic_body ` and a `flat_buffer`):  
```  
class MyParser : public http::response_parser<boost::beast::http::dynamic_body> {  
    using http::response_parser<boost::beast::http::dynamic_body>::parser;  
  
public:  
    std::size_t  
    put(boost::asio::const_buffer const& buffer, boost::system::error_code& ec) override {  
        BOOST_LOG_TRIVIAL(info) << buffer.data();  
        return parser::put(buffer, ec);  
    }  
};  
```  
Another possibility that I want to explore before going the "`Stream` way" is to define my own buffer type, do you think it's possible?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-25 15:17:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1208#issuecomment-407791426  

> override the put  
  
That's not going to work for a few reasons. `put` is not virtual, and the stream algorithms see `basic_parser&`, not your derived class. This is by design.  
  
> I found a bit overkill to define a full new Stream  
  
This is subjective. You should not be afraid of creating your own stream wrapper. It is straightforward, efficient, and easily tested in isolation. You don't even have to allocate memory like the `icy_stream`, since you can filter the mutable buffer sequence in-place.   
  
As I said, if you go this route I am more than happy to help. In fact, you could submit it as an improvement, located in the `<beast/experimental/http>` directory.

---

## Comment 4

> Username: HugoGuiroux  
> Created at: 2018-07-27 12:48:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1208#issuecomment-408408716  

I finally went the `Stream` way, which I still think is overkill: my version is mainly a copy/paste of the `boost::asio::ssl::stream` just to wrap the `async_read_some` method and add the filtering logic.  
  
My version is available [here](https://gist.github.com/HugoGuiroux/556a8564b40baa4d360d2f55d63302a6) for anyone who needs an example. Note that this has been tested with an SSL stream, both with `https` requests and `wss`.  
  
Thanks for your help.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-07-27 15:06:40 UTC  
> Updated at: 2018-07-27 15:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1208#issuecomment-408447046  

I took a look at your stream code. It isn't necessary to copy the entire interface of `ssl::stream`, you only need the read and write functions plus the accessors for the executor and next layer. This way, your wrapper can work for regular TCP/IP connections as well as SSL connections.  And your code will be unaffected by changes to the SSL stream.  
  
In your `read_op`, it is not necessary to use `handler_ptr`. This is because your operation has what we call a trivial state (cheap to move). You can move the members of the `data` struct directly into the `read_op` class. And you'll have to add the handler as a data member. It also appears that `data::match` is unused.  
  
Note that Beast provides this `ssl_stream` wrapper which supports move-construction and also works around a performance limitation of Asio's SSL stream:  
https://github.com/boostorg/beast/blob/e23ecc8ac903b303b9d1a9824b97c092cb3c09bd/include/boost/beast/experimental/core/ssl_stream.hpp#L28

---

## Comment 6

> Username: HugoGuiroux  
> Created at: 2018-07-27 15:33:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1208#issuecomment-408455410  

Thanks for the feedback! I am starting to wrap my head around the move notion in C++, so as you pointed out there are some inefficiencies in my code. I'll try to improve the code when I have the time.  
  
Regarding your remark about not copying the entire interface, are you saying that I only need to copy the methods that the HTTP/WS layer might call? One of the advantage of copying everything (if I understood correctly boost asio/beast) is that I can directly call methods like `async_shutdown()` on my stream instead of calling explicitly `next_layer().async_shutdown()` (but I might be wrong if there is some kind of forwarding that I didn't see).
