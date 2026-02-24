# #132 - Partial message reading [Closed]

> Username: e-fominov  
> Created at: 2016-10-14 08:03:33 UTC  
> Updated at: 2016-11-09 21:53:14 UTC  
> Closed at: 2016-11-09 21:53:14 UTC  
> Url: https://github.com/boostorg/beast/issues/132  

When making a server side, I want have two features:  
1. read message headers, analyze them and may be close connection before reading the body. This situation is similar to 100-continue (#123), but with different use-case  
2. read message body partially block-by-block when it is arriving in socket  
  
The use case is client sending very large file 2+ GB. If the file is too-large, I want to close connection with no reading body at all. Yes, client has many ways on how to split file, but If it does not split it - I want to server be alive when processing this request  
If file is large, but not much, I dont want to allocate 2GB per connection, but I want to have small buffer, about 64 kb that will be used to read parts of message body  
  
I can solve this situation partially, by making some custom message body class and use it in `request_v1<my_custom_body>` with custom `reader` class,  but I want to close connection before allocating a buffers if something is wrong in headers, but headers syntax is correct

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-14 10:23:54 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253763904  

I agree with everything you said. Another use-case is to read the headers, and then choose the type of body depending on the headers. I am working on a comprehensive solution.  
  
For now, if you want to abort the connection on bad headers, a work-around is to derive your own parser (make a copy of `parser_v1` and then change `on_headers`), and use `beast::http::async_parse` instead of `async_read`. I can help with that since there aren't any examples. In your parser, you can perform your checks in `on_headers` and set the error code:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/parser_v1.hpp#L218

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-10-14 10:34:08 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253766062  

The **Reader** should not be constructed until the headers are known

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-14 13:18:42 UTC  
> Updated at: 2016-10-14 13:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253796641  

@e-fominov How about something like this:  
  
```  
/** Read a message from the stream and process it  
*/  
template<bool isRequest, class Stream, class DynamicBuffer>  
void process_message(Stream& stream, DynamicBuffer& rb)  
{  
  using namespace beast::http;  
  
  // A header parser processes just the request/response line  
  // and headers, and doesn't need to know the Body type.  
  //  
  header_parser_v1<isRequest, headers> p0;  
  
  // Parse the request/response line and headers.  
  // http://vinniefalco.github.io/beast/beast/ref/http__parse/overload1.html  
  //  
  parse(stream, rb, p0);  
  
  // The header parser owns the message headers, you can read  
  // them using p0.get() or you can take ownership by calling p0.release().  
  //  
  message_headers<isRequest, headers> const& m0 = p0.get();  
  
  // Do any processing on m0...  
  
  // This function associates a Body type with the parser and  
  // returns a new parser which inherits the state machine of p0  
  //  
  auto p = with_body<string_body>(std::move(p0), ...);  
  
  // Continue parsing the message body into p,  
  // now that we have chosen a the body type.  
  parse(stream, p);  
  
  // Complete message is available now, take ownership:  
  message<isRequest, string_body, headers> m = p.release();  
  
  // Do something with m  
}  
```

---

## Comment 4

> Username: e-fominov  
> Created at: 2016-10-14 14:35:44 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253818000  

Should partial body reading be made on parser level or inside message body reader?  
  
```  
  // Continue parsing the message body into p,  
  // now that we have chosen a the body type.  
  parse(stream, p);  
  
```  
  
I want to read incoming 2GB video file sent by Qt functions by small 64-kb blocks with not allocating full size buffer

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-14 14:49:34 UTC  
> Updated at: 2016-10-14 14:53:50 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253821866  

> I want to read incoming 2GB video file sent by Qt functions by small 64-kb blocks with not allocating full size buffer  
  
Oh, you think that `read` and `parse` will issue a low-level socket read for the full 2GB? That would be pretty bad! It doesn't work that way. The size of the low-level read performed by `http::read` and `http::parse` is calculated by the choice of **DynamicBuffer**:  
  
Here's the read algorithm (from https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/read.ipp#L143)  
  
```  
            d.s.async_read_some(d.db.prepare(  
                read_size_helper(d.db, 65536)),  
                    std::move(*this));  
```  
  
The default implementation of `read_size_helper` respects the upper limit passed in the second parameter (which is 65536, or 64KB). And it examines the **DynamicBuffer** object (`d.db` in this case) to determine the most that can be accessed without requiring an additional allocation. See `capacity()` in the requirements for **DynamicBuffer** (http://vinniefalco.github.io/beast/beast/ref/DynamicBuffer.html).  
  
This means, that Beast already reads the data in up to 64KB chunks. If you want to change the strategy for controlling the read size, you can implement your own **DynamicBuffer** (which can just be a thin wrapper around `beast::streambuf`) and then provide an overload of `read_size_helper`. Or, if the default `read_size_helper` is sufficient, you can just customize the `capacity()` member function of your dynamic buffer class.  
  
I'd still like to hear your thoughts on the example code I posted for how the parsing of the headers might be separated from the parsing of the body - thanks.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-14 14:56:18 UTC  
> Updated at: 2016-10-14 14:56:46 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253824255  

@e-fominov Actually, looking at the implementation of `read_size_helper`:  
  
```  
template<class Allocator>  
std::size_t  
read_size_helper(basic_streambuf<  
    Allocator> const& streambuf, std::size_t max_size)  
{  
    auto const avail = streambuf.capacity() - streambuf.size();  
    if(avail == 0)  
        return std::min(max_size,  
            std::max<std::size_t>(512, streambuf.alloc_size_));  
    return std::min(max_size, avail);  
}  
```  
  
Note `streambuf.alloc_size_`. The default allocation size for `beast::basic_streambuf` is 1024. If you want to read in 64KB chunks you should construct the stream buffer with a larger block size, like this:  
  
```  
beast::streambuf sb{65536};  
```  
  
See:  
http://vinniefalco.github.io/beast/beast/ref/basic_streambuf/basic_streambuf/overload7.html

---

## Comment 7

> Username: e-fominov  
> Created at: 2016-10-14 15:43:29 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253838468  

@vinniefalco , yes, I have tested size limit. Looks like the best solution will be to have new body type with callback function. Beast internal reading is made with 1024-byte blocks, that is good enough for me  
  
> I'd still like to hear your thoughts on the example code I posted for how the parsing of the headers might be separated from the parsing of the body - thanks.  
  
First of all - it was a genious idea to split parser into `basic_parser_v1` and `parser_v1`. With current architecture its quite simple to make separate header parser. I think, that I can now make this solution myself and test it in my project.  
  
And I found one more solution - inside message body reader constructor I have a reference to a full message structure:  
  
```  
        reader(message<isRequest,  
                string_body, Headers>& m) noexcept  
            : s_(m.body)  
        {  
        }  
```  
  
So in a first call to `reader::write`  I can make all the checks that I need and return error code on invalid request

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-10-14 15:55:38 UTC  
> Updated at: 2016-10-14 15:57:29 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253842085  

> Beast internal reading is made with 1024-byte blocks, that is good enough for me  
  
Thinking about it, I'm not so sure. TCP/IP packet sizes are around 1400 bytes, this means you're doing two system calls on average for each packet. The reason I made this number so small is to reduce the memory requirements for server connections (10,000 connections would require half a gigabyte if the buffer was 64KB). Larger buffers reduce the total number of system calls required to process a message. You will probably want to do some performance testing to find a good number.  
  
>  it was a genious idea to split parser into `basic_parser_v1` and `parser_v1`.  
  
Thanks! I'm glad this works for you.  
  
>  I found one more solution - inside message body reader constructor I have a reference to a full message structure:  
  
Yes, when the **Reader** is constructed, you have access to the message. And there are guarantees on the lifetime of the message (see http://vinniefalco.github.io/beast/beast/ref/Reader.html).  
  
One thing I need to change, is that the **Reader** will only be constructed _after_ all of the headers get read in. This way you can perform calculations in the constructor that depend on the headers. The constructor for **Reader** also is not allowed to throw exceptions, I will update the documentation.  
  
---  
  
In **1.0.0-b16** (PR #125) the `on_headers` callback now returns an `enum` instead of `int`, and there's a new value `body_what::pause` which lets you return from the parser after the headers are read in and before the beginning of body. With this system, you can put your header business logic in the caller of `parse` or `read` instead of in the **Reader** (where, it really doesn't belong). Its up to you if you want to do that (_b16_ will be merged today).  
  
See:  
https://github.com/vinniefalco/Beast/blob/0a260be02c323e71d5895cf6c4b079a462aa10db/include/beast/http/basic_parser_v1.hpp#L100

---

## Comment 9

> Username: e-fominov  
> Created at: 2016-10-14 16:16:47 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253848846  

> One thing I need to change, is that the Reader will only be constructed after all of the headers get read in. This way you can perform calculations in the constructor that depend on the headers. The constructor for Reader also is **not allowed to throw exceptions**  
  
What if I need to return error from constructor and close the connection?  
  
May be it will be better to have additional `init` function inside Reader, like the [Writer](https://github.com/vinniefalco/Beast/blob/message/include/beast/http/string_body.hpp#L72) has? And you can make optional call to it if Reader has this function.   
But I need to have a full message reference as a parameter + error code

---

## Comment 10

> Username: e-fominov  
> Created at: 2016-10-14 16:36:02 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253853364  

One more question.  
  
I want to make a file_body that will have a Reader saving data to a temporary file, so the result body will be file name/handle.   
How can I pass an argument with temporary directory name to a Reader? New parser only?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-10-14 16:43:07 UTC  
> Updated at: 2016-10-14 16:44:07 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-253855075  

If you want to pass arguments to the **Reader** you need to put them in the **Body::value_type**. For example:  
  
```  
struct writable_file_body  
{  
  struct value_type  
  {  
    boost::filesystem::path dir;  
    boost::filesystem::path filename;  
  };  
  
  struct reader;  
};  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2016-10-17 17:30:36 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-254276023  

The changes to headers parsing are in #146

---

## Comment 13

> Username: georgi-d  
> Created at: 2016-10-25 15:31:24 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-256070198  

I really like the library but am still missing one critical functionality, to be able to read the message one chunk (HTTP chunk on the wire) at a time. I would want to have a parser for which async_parse can be called multiple times to fill the message one chunk at a time. After each completion of async_parse I should be able to either drain the message body to reduce memory usage or leave it there so the next async_parse would append to the body buffer. I think this functionality is available for websockets.  
  
Another useful feature would be to be able to specify async_parse to complete after reading at most X amount of bytes in the body. Calling async_parse again should continue appending data to the message body.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2016-10-25 16:11:14 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-256082152  

@georgi-d Please open this as a new issue and I will answer, thanks!

---

## Comment 15

> Username: georgi-d  
> Created at: 2016-10-25 16:22:57 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-256085287  

I have opened  #154   
  
Thanks

---

## Comment 16

> Username: vinniefalco  
> Created at: 2016-11-09 21:53:14 UTC  
> Url: https://github.com/boostorg/beast/issues/132#issuecomment-259537188  

This has been addressed as of _1.0.0-b17_
