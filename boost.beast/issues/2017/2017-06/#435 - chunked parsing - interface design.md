# #435 - chunked parsing - interface design [Closed]

> Username: qwertzui11  
> Created at: 2017-06-08 08:13:09 UTC  
> Updated at: 2017-06-14 00:38:21 UTC  
> Closed at: 2017-06-14 00:38:21 UTC  
> Url: https://github.com/boostorg/beast/issues/435  

### version   
master / v50  
  
### code  
```  
beast::http::parser<false, beast::http::dynamic_body, beast::http::fields>  
          parser;  
beast::http::read_some(socket, stream_buffer, parser);  
const auto body_size =  
  boost::asio::buffer_size(parser.get().body.data());  
if (body_size == 0)  
  ; // we parsed a chunk header  
else  
  ; // we got a (maybe incomplete) chunk body  
parser.get().body.consume(body_size);  
```  
  
### problem  
I'm trying to read a http request chunk by chunk. Previous versions of beast had the method `state()` which returned the state of the parser. In previous versions i checked `parser.state() == beast::parser::state::chunk_header`, then I knew the parser was waiting for a header and a !complete! body chunk is available. I suggest to make the `state()` method public again (and handle the state `chunk_header0` somehow) or do something is_chunk_available. Have I missed something, is there a better for chunked reading? What do you think?  
Another suggestion I got is, that read_some only shall return on a state change, or an error?  
I like it, that read_some doesn't return the bytes to consume no more. :+1:

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 12:37:05 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307090217  

I think what you really want is to use `buffer_body` instead, then you can just set up a static buffer (it could even be a stack array).  To use it, just set `body.data` to point to some memory, and set `body.size` to the size of that memory. When you call `read_some` (or you could even call `read` it would work better by trying to fill the whole buffer) it will write to the memory and adjust the data and size values in the body to "consume" them. When the buffer is full you'll get `error::need_buffer`, and you can just reset the buffer and then call read again.  
  
The documentation has an example:  
http://vinniefalco.github.io/beast/beast/http_examples/http_relay.html  
  
See:  
http://vinniefalco.github.io/beast/beast/ref/http__buffer_body.html  
  
Hopefully you won't need the state. Applications shouldn't care about knowing where the chunk boundaries are, any node along the path between the client and server can reframe the message payload, including removing the chunked encoding entirely (and putting in a Content-Length).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-08 20:06:26 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307212593  

@qwertzui11 Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!

---

## Comment 3

> Username: qwertzui11  
> Created at: 2017-06-09 09:10:07 UTC  
> Updated at: 2017-06-09 09:12:55 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307337921  

I will definitely participate, spoiler it will be very positive one :wink:   
Your suggested solution seems to solve #154 . But my scenario is a bit different. I've got to work with a legacy system, where chunked http gets abused as "websocket". Every chunk is a message and I've got to read from the stream "message by message" or "chunk by chunk". I've got know where a chunk starts, ends and its size.  
  
> Hopefully you won't need the state. Applications shouldn't care about knowing where the chunk boundaries are, any node along the path between the client and server can reframe the message payload, including removing the chunked encoding entirely (and putting in a Content-Length).  
  
I'm afraid that's the case

---

## Comment 4

> Username: qwertzui11  
> Created at: 2017-06-09 09:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307341487  

> Hopefully you won't need the state. Applications shouldn't care about knowing where the chunk boundaries are, any node along the path between the client and server can reframe the message payload, including removing the chunked encoding entirely (and putting in a Content-Length).  
  
I don't think beast should handle such dirty, non-standard corner-cases.   
Thx for your time, I'll solve my issue in another way.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-09 11:50:13 UTC  
> Updated at: 2017-06-09 11:54:32 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307368472  

You can subclass `basic_parser` (its easy) and then you get a callback at the beginning of a chunk, it gives you the extension if any and the size in advance. See `on_body`  
http://vinniefalco.github.io/beast/beast/http/custom_parsers.html  
  
Check out how easy it is to subclass `basic_parser`  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/parser.hpp#L41

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-09 11:53:25 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307369077  

>I don't think beast should handle such dirty, non-standard corner-cases.  
  
I disagree, Beast is supposed to be foundational / low-level. We will figure this out!

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-06-11 16:16:33 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-307639568  

Following up, what do you think about these solutions?

---

## Comment 8

> Username: qwertzui11  
> Created at: 2017-06-13 11:50:57 UTC  
> Updated at: 2017-06-13 11:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308091824  

> You can subclass basic_parser (its easy) and then you get a callback at the beginning of a chunk, it gives you the extension if any and the size in advance. See on_body  
  
I really like this solution! However `on_chunk` never gets called. All over the parser.ipp code `if(*p == ';')` gets checked before any `on_chunk` call. I'm no expert, but I guess this has something to do with "chunk decoration"? so `on_chunk` never gets called without decoration, although documentation suggests otherwise. `/// Called for each chunk header.`  
In my tests `on_data` gets called with chunk data or sometimes parts of a chunk.  
Have I got something wrong? Shall I upload my sample code?   
  
https://github.com/vinniefalco/Beast/blob/fde69298dce9d29c13eac272d34216e972bfb2fc/include/beast/http/impl/basic_parser.ipp#L560  
https://github.com/vinniefalco/Beast/blob/fde69298dce9d29c13eac272d34216e972bfb2fc/include/beast/http/impl/basic_parser.ipp#L605  
  
version is master (v55)  
Thx for your time!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-06-13 11:56:16 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308092928  

How lame, another bug. A call to `on_chunk` for the case when the extension is empty is missing.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-06-13 11:56:54 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308093073  

Chunk decoration is for outgoing not incoming messages. Its how you **set** the extensions (and trailers).

---

## Comment 11

> Username: qwertzui11  
> Created at: 2017-06-13 11:58:46 UTC  
> Updated at: 2017-06-13 11:58:53 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308093467  

> Chunk decoration is for outgoing not incoming messages. Its how you set the extensions (and trailers).  
  
ah, check! :+1:

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-06-13 11:59:59 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308093731  

At least the fix was easy

---

## Comment 13

> Username: qwertzui11  
> Created at: 2017-06-13 12:05:38 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308094885  

I pulled an ran my tests successfully. I like the custom parser solution. I'm now able to read the stream chunk by chunk, and beasts handles all the http overhead.  
For other people: I'm getting the the "chunk header" information by `on_chunk` and the "chunk body" via `on_body`  
Thx again.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-06-13 12:07:22 UTC  
> Url: https://github.com/boostorg/beast/issues/435#issuecomment-308095284  

GitHub will close this issue automatically when the commit is merged (it says "fix #435")
