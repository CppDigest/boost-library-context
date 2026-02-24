# #2111 - Reading multipart response part by part [Closed]

> Username: rhegner  
> Created at: 2020-10-21 15:39:12 UTC  
> Updated at: 2021-05-29 17:10:53 UTC  
> Closed at: 2021-05-29 17:10:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2111  

Here's what I'm trying to achieve:  
  
1. Read the response header using `http::async_read_header`  
2. Then repeatedly do the following  
   1. Asynchronously wait for the the multipart boundary  
   2. Read the header of the part using `http::async_read_header` (which includes a Content-Length header for the part)  
   3. Asynchronously read the binary content of the part into a pre-allocated buffer based on the Content-Length from the step above.  
  
I'm completely new to boost::beast, so I'm struggling a bit. Here are my questions:  
  
1. What's the best way to asynchronously wait for and read the next multipart boundary?  
2. Is it possible to use `http::async_read_header` for the part headers? Or does it work only for the main response header?  
3. How to I read a fixed amount of binary data into a pre-allocated buffer?  
  
Any help would be greatly appreciated!!

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-21 15:43:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-713670362  

Hi @rhegner ,  
  
I'm happy to write a little demo of how to do this for you.  
  
Would you kindly:  
  
1. Provide me with a sample message to use as a test input.  
2. let me know your preferred completion strategy (callbacks & shared pointer, coroutines, etc).  
  
R

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-21 15:54:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-713677508  

You can't use Beast's parser to read the boundary in a multi-part message, because those boundaries don't have a request or response line.

---

## Comment 3

> Username: kervinpierre  
> Created at: 2020-10-21 21:32:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-713889798  

_How to I read a fixed amount of binary data into a pre-allocated buffer_?  
  
async_read_some()?  Which is what I'd try for this. async_read_some() with a buffer_body parser.  This way you can detect the boundaries yourself if you really liked.

---

## Comment 4

> Username: rhegner  
> Created at: 2020-10-22 20:49:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-714753922  

Hi all, thanks for your responses so far. Here is a sample response. As you can see this is a MJPEG stream and the goal is to handle the live stream image by image with a callback per image. Obviously the content of the parts is not really jpeg binary but just dummy characters.  
  
```  
HTTP/1.1 200 OK  
Date: Thu, 22 Oct 2020 20:26:01 GMT  
Content-Type: multipart/x-mixed-replace;boundary="FrameSeparator"  
  
  
--FrameSeparator  
Content-Type: image/jpeg  
Content-Length: 3  
  
abc  
--FrameSeparator  
Content-Type: image/jpeg  
Content-Length: 3  
  
def  
--FrameSeparator--  
  
```  
  
@madmongo1 I'm a little bit familiar with boost::asio and there I'm usually callbacks with either raw or shared pointers. I'd really appreciate some example code!

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-10-23 06:43:35 UTC  
> Updated at: 2020-10-23 06:47:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-714986247  

Hi @rhegner   
  
I've added a fully functioning demo to a repo I use for writing tutorial and demonstration code.  
  
project is here: https://github.com/test-scenarios/boost_beast_websocket_echo/tree/master/pre-cxx20/mime_reader  
  
You will notice that I have implemented a composed operation for iterating through the mime parts. You pass it a callback function, which will be executed within the context of the composed operation (i.e. on your handler's executor).  
  
You should not touch the stream object during the execution of this function. Note also that it does not need to claim a shared_ptr because when this function is executed, the asynchronous completion handler supplied to `mime_reader::async_read_mime` has not yet been invoked.  
  
Demo output given your test data:  
  
```  
[2020-10-23 08:35:24.531] [info] received part length: 3, type: image/jpeg, contents:  
abc  
  
[2020-10-23 08:35:24.531] [info] received part length: 3, type: image/jpeg, contents:  
def  
  
  
(Process finished with exit code 0)  
```  
  
dependencies: spdlog, fmtlib - you can remove all references to these if you don't want to use them.  
  
note that this function should really be called `read_all_parts`. I made a design decision change halfway through and forgot to modify this.  
  
```  
    void  
    read_next_part()  
    {  
        auto self = shared_from_this();  
  
        mime_reader::async_read_mime(  
            stream_,  
            boundary_,  
            [](mime_reader::mime_part part) {  
                spdlog::info(  
                    "received part length: {}, type: {}, contents:\n{}\n",  
                    part.headers[http::field::content_length],  
                    part.headers[http::field::content_type],  
                    part.body);  
            },  
            net::dynamic_buffer(rx_storage_),  
            net::bind_executor(  
                get_executor(), [self](error_code ec) {  
                    if (ec)  
                        spdlog::error("mime error: {}", ec.message());  
                    else  
                        spdlog::debug("parts finished");  
                }));  
    }  
```  
  
Usual caveats:  
  
* I wrote this code early in the morning before coffee  
* I certainly haven't made it robust for production code  
* Please review and understand its limitations before blindly shoehorning it into a flight control system or anything else important.  
* Not warrantied for any purpose whatsoever  
* Copyright (C) Richard Hodges 2000 hodges.r@gmail.com  
* Published under the boost licence: you may use to your heart's content for any purpose whatsoever without involving me.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-10-26 16:08:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-716651755  

May I close this issue?

---

## Comment 7

> Username: rhegner  
> Created at: 2020-10-26 16:14:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-716655937  

@madmongo1 thanks a lot for your sample code! This is very helpful!  
I haven't provided any feedback yet because I have not yet been able to fully integrate the code with my application.  
I'll close it for now.  
Thanks again!

---

## Comment 8

> Username: rhegner  
> Created at: 2020-10-26 19:13:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-716765210  

Ok I closed the issue too early. What I didn't realize initially is that the stream is sent using `Transfer-Encoding: chunked`, which makes sense, but my browser abstracted that away when I copied the example response above.  
  
On the line, the stream actually looks like this, where `43` and `3` are the chunk headers:  
  
```  
HTTP/1.1 200 OK  
Date: Mon, 26 Oct 2020 18:29:07 GMT  
Content-Type: multipart/x-mixed-replace;boundary="FrameSeparator"  
Server: Kestrel  
Cache-Control: no-store  
Transfer-Encoding: chunked  
  
43  
  
--FrameSeparator  
Content-Type: image/jpeg  
Content-Length: 3  
  
  
3  
abc  
```  
  
The beast documentation says that the http parser automatically removes the chunked transfer coding. But in your solution it looks like after reading the initial header you're switching from using beast to using asio, which presumably does not hide the chunking.  
  
In the example above the multipart header and body come in their separate chunk, but I guess in real life these boundaries do not always align, so I think we need to do a layered approach where one layer (the parser) does the de-chunking and then the next layer does the multipart handling.  
  
So... is there a way to use the beast parser (instead of falling back to the asio stream) to parse the multipart response?

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-10-26 19:20:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-716769099  

Yes, beast will automatically decode a chunked message. However, in order to take advantage of this you would need to implement the composed operation above in terms of `beast::http::async_read_some` rather than `asio::async_read_until`. Because you want beast to do the work of decoding the chunks, but you still want access to the dynamic buffer in order to parse it for multipart boundaries.  
  
https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_some.html

---

## Comment 10

> Username: rhegner  
> Created at: 2020-10-27 20:15:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-717512609  

I'm still struggling with this. I had a close look at the echo example which reads the input line-by-line (which would be a good building block for reading the multipart boundary and the part headers), but it does so using `stream_.async_read_some` which does not use a parser, and therefore does not handle the chunked transfer - or at least that's my understanding.  
  
I could not figure out how to write a similar method for reading a line until CRLF on top of `beast::http::async_read_some` which takes a buffer and a parser. I guess I'd have to use two dynamic buffers? The one I'm passing to `beast::http::async_read_some`, and one for managing any content past the CRLF marker? Is using a `response_parser<dynamic_body>` the way to go?  
  
I need to get this MJPEG streaming stuff working by friday, and I'm pretty lost here. So any further help would be greatly appreciated!  
  
Even just a small building block like a composed `async_read_line` (which reads a string up to the next CRLF) on top of `beast::http::async_read_some` using a parser would be really helpful

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-10-27 21:03:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-717537957  

In the response above I linked the docs of the version of async_read_some which does use the parser.  
  
It’s late for me now but I can revisit the example in the morning.   
  
Can you provide another sample of input please?

---

## Comment 12

> Username: rhegner  
> Created at: 2020-10-27 22:10:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-717570205  

That would be great! I spent the entire evening trying, but didn't make much progress.  
  
This is an example of the raw TCP stream (where you can see the chunking):  
  
```  
HTTP/1.1 200 OK  
Date: Tue, 27 Oct 2020 22:01:59 GMT  
Content-Type: multipart/x-mixed-replace; boundary=jpgboundary  
Server: Kestrel  
Cache-Control: no-store  
Transfer-Encoding: chunked  
  
  
--jpgboundary  
Content-Type: image/jpeg  
Content-Length: 3  
  
  
3  
abc  
2  
  
  
40  
  
--jpgboundary  
Content-Type: image/jpeg  
Content-Length: 3  
  
  
3  
def  
2  
  
  
13  
  
--jpgboundary--  
  
0  
```  
After de-chunking the body would look like this:  
  
```  
HTTP/1.1 200 OK  
Date: Tue, 27 Oct 2020 22:01:59 GMT  
Content-Type: multipart/x-mixed-replace; boundary=jpgboundary  
Server: Kestrel  
Cache-Control: no-store  
Transfer-Encoding: chunked  
  
  
--jpgboundary  
Content-Type: image/jpeg  
Content-Length: 3  
  
abc  
  
--jpgboundary  
Content-Type: image/jpeg  
Content-Length: 3  
  
def  
  
--jpgboundary--  
  
```

---

## Comment 13

> Username: madmongo1  
> Created at: 2020-10-28 05:11:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-717702591  

Is that test data correct? I don't see a chunk size for the first chunk. I'm testing an update to my solution.

---

## Comment 14

> Username: rhegner  
> Created at: 2020-10-28 06:31:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-717729756  

Hmm you're right, that's strange.... - I guess I was too tired last night. I ran my response dump tool again and now it looks ok:  
  
```  
HTTP/1.1 200 OK  
Date: Wed, 28 Oct 2020 06:28:14 GMT  
Content-Type: multipart/x-mixed-replace; boundary=jpgboundary  
Server: Kestrel  
Cache-Control: no-store  
Transfer-Encoding: chunked  
  
40  
  
--jpgboundary  
Content-Type: image/jpeg  
Content-Length: 3  
  
  
3  
abc  
2  
  
  
40  
  
--jpgboundary  
Content-Type: image/jpeg  
Content-Length: 3  
  
  
3  
def  
2  
  
```

---

## Comment 15

> Username: madmongo1  
> Created at: 2020-10-28 06:56:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-717740511  

thanks. In writing the solution I think I've uncovered a bug in beast. I'm going to write a test for that and solve it than then come back to this.

---

## Comment 16

> Username: madmongo1  
> Created at: 2020-10-29 06:38:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-718394827  

ok, solved. wasn't a bug. Just an errpr in my test driver. fixing and will publish today.

---

## Comment 17

> Username: madmongo1  
> Created at: 2020-10-29 11:09:12 UTC  
> Updated at: 2020-10-29 11:10:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-718670091  

@rhegner   
  
Please find the solution linked. I have flattened the logic into a test. Obviously in production code you would yield the current async operation at each point where I have used `spin()`.  
  
This solution handles both chunked and non-chunked scenarios.  
  
https://github.com/test-scenarios/boost_beast_websocket_echo/tree/master/pre-cxx20/mime_reader  
  
@vinniefalco would you mind taking a look at the convoluted logic I have used when collecting data from chunks? I had to do this because async_read_some sets bytes_transferred to 0 when reading body data and when reading a chunk body, does not update the parser's body's size. That feels wrong to me, but happy to be corrected.  
  
e.g.:  
```  
        auto on_chunk_body =  
            [&](std::uint64_t remain, beast::string_view body, error_code &ec) {  
                auto pos = outbuf.size();  
                auto len = body.size();  
                outbuf.grow(len);  
                net::buffer_copy(outbuf.data(pos, len),  
                                 net::buffer(body.data(), len));  
                expect_chunk_header = (remain == 0);  
                return len;  
            };  
        parser.on_chunk_body(on_chunk_body);  
```  
  
later used thus:  
  
```  
            if (parser.chunked())  
            {  
                while (expect_chunk_header)  
                {  
                    http::async_read_some(local, rxbuf, parser, handler);  
                    spin("skipping chunk header");  
                }  
                http::async_read_some(local, rxbuf, parser, handler);  
                spin("seeking boundary");  
            }  
            else  
            {  
                outbuf.grow(512);  
                auto dat = outbuf.data(outbuf.size() - 512, outbuf.size());  
                parser.get().body().data = dat.data();  
                parser.get().body().size = dat.size();  
                parser.get().body().more = false;  
                http::async_read_some(local, rxbuf, parser, handler);  
                spin("seeking boundary");  
                outbuf.shrink(512 - parser.get().body().size);  
            }  
```

---

## Comment 18

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-751240920  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 19

> Username: stale[bot]  
> Created at: 2021-05-29 17:10:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2111#issuecomment-850866703  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
