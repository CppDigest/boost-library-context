# #254 - Example for reading header and body separately [Closed]

> Username: mika-fischer  
> Created at: 2017-02-06 08:09:34 UTC  
> Updated at: 2017-05-09 14:20:22 UTC  
> Closed at: 2017-05-09 14:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/254  

I have the following use-case:  
  
1. Read `request_header`  
2. Decide whether request  
2.1. Has no body  
2.2. Has a small body that can just be read into a buffer  
2.3. Has a large body that needs to be streamed  
3. Do the appropriate thing  
  
I can't really figure out how to do step 3 with beast, although I'm sure it must be possible somehow.  
  
Ideally, after reading the `request_header` I'd like to be able to create an object using the `request_header` I already read and that I can call `async_read*` on to read all or parts of the body. If there's no body, or I reach the end of it, I want to get EOF.  
  
Is this possible right now?

---

## Comment 1

> Username: mika-fischer  
> Created at: 2017-02-06 09:54:49 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-277634851  

Ok, I can see that when I use the parser instead of messages I can get part of the way there:  
  
```cpp  
using req_header_parser = beast::http::header_parser_v1<true, beast::http::fields>;  
  
template<typename Body>  
using req_body_parser = beast::http::parser_v1<true, Body, beast::http::fields>;  
  
req_header_parser header_parser;  
async_parse(socket_, buffer, header_parser, yield);  
  
if (/* how to find out that there's a body */)  
{  
    if (streaming_needed)  
    {  
        // How to do this?  
    }  
    else // Just read whole body into buffer  
    {  
        req_body_parser<beast::http::string_body> body_parser(header_parser);  
        async_parse(socket_, buffer, body_parser, yield);  
    }  
}  
```  
  
So my two remaining questions are:  
1. How to determine from `request_header` if there's a body or not.  
2. How to `async_read_some` the message body piece by piece with EOF at the end of the body.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-02-06 11:28:54 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-277655758  

You've stumbled onto a current limitation of Beast. Its the last remaining design problem before I submit this library for a Boost review, and I'm trying to figure out how best to accomplish this. There's a discussion thread about it here:  
https://github.com/vinniefalco/Beast/issues/154

---

## Comment 3

> Username: mika-fischer  
> Created at: 2017-02-06 11:59:45 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-277661700  

Ah, that's too bad. Otherwise it looks quite nice!  
  
Do I understand correctly that:  
1. Reading a request piece by piece with flow control is currently impossible with the `Reader` interface (short of blocking the event loop).  
2. Writing a response piece by piece is possible, though somewhat clumsy with the inverted flow control of the `Writer` interface.  
3. You're working on a redesign solving these problems, so it doesn't make much sense at this time to come up with workarounds for the current situation and instead it would be more prudent to wait for the new interface.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-02-06 12:01:56 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-277662119  

That is all almost correct. For 3 I think you can read the header using Beast and then read the body using your own loop. Beast comes with a chunk decoder. The caveat is that you will have to parse the header fields yourself (for example, check Transfer-Encoding). Its not terribly difficult. So if you absolutely can't wait, there's a solution (although kind of ugly).

---

## Comment 5

> Username: mika-fischer  
> Created at: 2017-02-07 09:07:29 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-277940557  

I think we might get by with the Reader and Writer interface for now. But parsing the header before the body is important, and still not working reliably, see #257.

---

## Comment 6

> Username: mika-fischer  
> Created at: 2017-02-08 11:35:15 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-278304036  

OK, I think I was wrong. It's not workable with the current API. I think for a low-level HTTP API, there needs to be a way to let the library user control how the body of the request is read and how the reply is written. At the same time, I don't want to mess around with HTTP internals too much, or do the chunk-de/encoding myself.  
  
What I want to do (in pseudocode) is:  
```cpp  
raw_request req;  
async_read(socket, req, yield); // Reads headers and stops  
  
raw_response res(req); // Sets correct HTTP version, 200, OK by default  
prepare(res); // Set chunk-encoding if no content-length given  
async_write(socket, res, yield); // Writes response headers and stops  
  
some_buffer_t buffer(buffer_size);  
error_code ec;  
while (true)  
{  
    size_t n_read = req.body.async_read(buffer, yield[ec]); // end of body gives eof &  
                                                            // decodes chunks  
    if (ec && ec != eof)  
        throw system_error(ec);  
    if (n_read > 0)  
    {  
        some_buffer_t transformed_data = transform(buffer, n_read);  
        res.body.async_write(transformed_data, yield); // encodes chunks  
    }  
    if (ec == eof)  
    {  
        res.body.end();  
        break;  
    }  
}  
```  
  
Does this look reasonable to you? Is it a use-case you would like beast to support?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-02-08 11:38:45 UTC  
> Updated at: 2017-02-08 11:39:42 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-278304712  

>Is it a use-case you would like beast to support?  
  
Yes, absolutely. Beast intends to be a low level library, that developers can use to build on top of. It needs to support "Expect: 100-continue", relaying, and customization of the socket loop that reads and writes the body. We are aware of this issue, and its been the subject of development for the last several weeks, see https://github.com/vinniefalco/Beast/issues/154  
  
Your example code is missing a lot of important details but I agree in principle with the ideas. It should be possible to use Beast to read the header, and then have Beast provide assistance in reading the body using your own socket loop.  
  
You can observe the design and development of the new interfaces here:  
https://github.com/vinniefalco/Beast/blob/http/test/http/new_parser.cpp#L73

---

## Comment 8

> Username: mika-fischer  
> Created at: 2017-02-08 18:22:32 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-278416528  

Alright, we'll just use websockets for this particular use-case and wait for the new parser. Other than this issue, I like beast a lot, great work!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-04-28 01:35:59 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-297883914  

@mika-fischer The HTTP changes are in review, feel free to comment if you'd like:  
https://github.com/vinniefalco/Beast/pull/315

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-05-09 14:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/254#issuecomment-300180024  

This is merged to _master_
