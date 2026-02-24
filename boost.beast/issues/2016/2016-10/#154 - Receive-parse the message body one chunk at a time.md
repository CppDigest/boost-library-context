# #154 - Receive/parse the message body one chunk at a time [Closed]

> Username: georgi-d  
> Created at: 2016-10-25 16:21:44 UTC  
> Updated at: 2017-06-12 03:09:50 UTC  
> Closed at: 2017-06-12 03:09:50 UTC  
> Url: https://github.com/boostorg/beast/issues/154  

I really like the library but am still missing one critical functionality, to be able to read the message one chunk (HTTP chunk on the wire) at a time. I would want to have a parser for which async_parse can be called multiple times to fill the message one chunk at a time. After each completion of async_parse I should be able to either drain the message body to reduce memory usage or leave it there so the next async_parse would append to the body buffer. I think this functionality is available for websockets.  
  
Another useful feature would be to be able to specify async_parse to complete after reading at most X amount of bytes in the body. Calling async_parse again should continue appending data to the message body.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-10-25 16:28:30 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256086859  

Incremental processing for the body is already possible, but with a different interface than what you are asking for. If you want to process the body incrementally then you simply need to create your own **Body** type, and then customize the **Reader**:  
  
Requirements for **Body**  
http://vinniefalco.github.io/beast/beast/ref/Body.html  
  
Requirements for **Reader**  
http://vinniefalco.github.io/beast/beast/ref/Reader.html  
  
Example user-defined body:  
  
```  
// incrementally reads body data  
struct MyBody  
{  
  using value_type = std::string; // can be any type you want  
  
  struct reader  
  {  
    template<bool isRequest, class Headers>  
    reader(message<isRequest, MyBody, Headers>& m);  
  
    void  
    write(void const* data, std::size_t size, error_code& ec)  
    {  
      // this will be called with each piece of the body, after chunk decoding  
  };  
};  
```  
  
> Another useful feature would be to be able to specify async_parse to complete after reading at most X amount of bytes in the body  
  
Why do you want to do this? What is the use-case?

---

## Comment 2

> Username: georgi-d  
> Created at: 2016-10-25 17:53:35 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256110022  

The Reader customization is a nice feature but as well as any other callback mechanisms it does not provide the functionality to suspend/throttle the receive until another async operation completes.  The only option is to store the whole message body and then start a different async operation.  
  
Imagine a reverse proxy implementation that receives an HTTP message from a client parses the headers and then based on the path creates a new http request to a backend server then passes all the data received from the client to the server.  
  
The workflow would be  like this:  
  
```  
{    
  parse_headers(sock, hp, msg);  
  // check the path  
  connect(backend, beSock);  
  error_code ec;  
  request beReq;  
  
  for (parse_atmost(sock, bp, msg, 1024);   
         ec == error_more_data;  
        parse_atmost(sock, bp, msg, 1024))  
  {  
      write_chunk(beSock, beReq, msg.data());  
  }  
}  
```  
  
I think an error code can be used to indicate that the parsing was stopped before reaching the end of the message.  
  
To make things symmetrical a similar mechanism would be needed on the write side as well. To send the data chunk by chunk.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2016-10-25 18:09:27 UTC  
> Updated at: 2016-10-25 18:11:06 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256114332  

I agree, **Reader** does not provide the ability to throttle. However, I think the responsibility of throttling lies not with the HTTP implementation but rather, the choice of the **AsyncReadStream** parameter used to parse:  
http://vinniefalco.github.io/beast/beast/ref/http__async_parse.html  
  
If you want to throttle, then you should write a class that meets the requirements of **AsyncReadStream** (http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/AsyncReadStream.html), which wraps an existing stream and allows calls to `async_read_some` to be suspended and resumed.  
  
For the write side you can use a custom body, and suspend by returning `boost::indeterminate` from the **Writer** instance:  
http://vinniefalco.github.io/beast/beast/ref/Writer.html

---

## Comment 4

> Username: georgi-d  
> Created at: 2016-10-25 18:39:10 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256132795  

I do not really agree that throttling should be responsibility of the read stream. The whole design of ASIO is to have throttling be done by not issuing new async_\* operations. If the reverse proxy was implemented over TCP then we would just have one buffer of fixed length. Then async_read into it, then async_write out of it. Throttling is automatic.   
  
Could you add an example of how a simple reverse proxy would be implemented? The backend server can easily be fixed during construction and not depend on the request.  
  
 I feel the implementation would be much simpler by having a custom parser that can be called multiple times than implementing a custom AsyncReadStream.  
  
Thanks

---

## Comment 5

> Username: vinniefalco  
> Created at: 2016-10-25 19:07:00 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256144578  

I'll consider pausing the parser during the message body.  
  
What about the **Writer** solution, does that work for you?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-10-25 19:12:22 UTC  
> Updated at: 2016-10-25 19:12:56 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256146055  

@georgi-d As a temporary solution, you could first read the headers using the `headers_parser`:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/headers_parser_v1.hpp#L39  
  
And then, construct a new parser for the body using `with_body`:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/parser_v1.hpp#L286  
  
The resulting parser will be ready to read the message body, and you can just feed in the data. In other words read from the socket yourself and pass the buffers in to `parser_v1::write()`. When the parser is done, `parser_v1::complete()` will return `true`.  
  
The implementation of `beast::http::parse` is pretty straightforward, there's no magic going on:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/parse.ipp#L220

---

## Comment 7

> Username: georgi-d  
> Created at: 2016-10-25 19:54:10 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256157075  

I like the idea of reading the data myself and passing it to the parser with write. I will give it a try.   
  
As for the Writer solution I will try it as well and see how it works for me. I would really like if there is a solution similar to the parser_v1::write for writing the message body so I can control it from the outside rather than being called back for the data.  
  
Thanks

---

## Comment 8

> Username: vinniefalco  
> Created at: 2016-10-25 22:31:11 UTC  
> Updated at: 2016-10-25 22:31:27 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256196936  

For the `write` you can specify the type `beast::http::empty_body` for **Body** in the message, call `beast::http::write` to send the headers, and then handle writing of the body yourself, directly on the socket. You won't be able to call `prepare` (since it will set a zero Content-Length) but that's not a big deal, you can just fill those fields in yourself (you'll also have to set Connection and Transfer-Encoding).  
  
You can still do chunked encoding, Beast provides a routine for you:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/detail/chunk_encode.hpp#L88  
  
I can make `chunk_encode` part of the public API if necessary.  
  
This would be much easier than defining your own **Body** and associated **writer**.

---

## Comment 9

> Username: georgi-d  
> Created at: 2016-10-26 12:06:28 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-256327803  

This sounds good, I will give it a try. Making chunk_encode public would be useful I believe.  
  
Thanks

---

## Comment 10

> Username: georgi-d  
> Created at: 2016-11-10 19:27:02 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259783576  

I started working on reading myself from the socket and passing the data to the parser and ended up copying 90% of async_parse + parse_op. The only extra feature I actually need is the ability to break out of the async_read loop and call the handler on a condition different than parse.complete() returning true.   
  
I was thinking about adding a version of async_parse similar to  [boost::asio::async_read](http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/async_read/overload2.html) which receives a CompletionCondition functor. The CompletionCondition should be called after each parser.write() with the number of bytes consumed by the parser and and an error_code (optionally a reference to the parser but it could also be a binded parameter). The default CompletionCondition could be implemented as returning the result of parser.complete().  
  
On error or parser.complete() returning true the handler should also be called.   
  
async_parse() should be callable multiple times for a single http message until the complete message body is consumed and parsed. From I gather the state in parse_op would not need to change to support multiple invocations. On each invocation the data.state variable would start from 0.  
  
Thanks

---

## Comment 11

> Username: vinniefalco  
> Created at: 2016-11-10 19:44:02 UTC  
> Updated at: 2016-11-10 19:46:11 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259787831  

Why don't you just create your own **Parser** ? Then you can return whatever value you want from `complete()`. And you can call `async_parse` over and over again.

---

## Comment 12

> Username: georgi-d  
> Created at: 2016-11-10 20:47:33 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259802923  

I  thought about it but feels more like a hack to me. The parser's job is to parse the message and not to determine when the read operation should be completed and the handler invoked. The fact that it currently is used as the sole condition for completion of the async_parse is a special case in my opinion. It is similar to using boost::asio::async_read() with boost::asio::transfer_all() condition. If the parser is used to indicate that the async_read should complete mid message then it should have some other "complete()" to indicate if the message has really ended or not. It should also have a special "reset_read()" method to enable consecutive async_parse () calls.  
  
I will try implementing both solutions to see which one I like better for the use case I have.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2016-11-10 20:51:40 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259803922  

@georgi-d It is my intention for `parse` and `async_parse` to be something of a "generic read algorithm" that isn't necessarily constrained to only parsing HTTP messages (although thats what the library predominantly uses it for). The `parse_op` composed operation takes care of some of the heavy lifting for you, so that you can focus on implementing the business logic.  
  
I have been thinking a little bit about renaming `async_parse` to `async_read`, rename **Parser** to **Reader**, and move the free function and composed operation to `<beast/core/read.hpp>` since it is now quite general purpose. I don't know that I will actually do this in the near future but that is my thinking.  
  
Given this direction, I don't think you should view using the **Parser** concept in that fashion as a hack. Beast is a low level library, and exposes building blocks for you to use. There's no "right" or "wrong" way to put together these building blocks. I want the blocks to be flexible, so people can build things with them that I did not anticipate or design for. I think that's the best measure of "success" for this library.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2016-11-10 20:53:26 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259804348  

If you wouldn't mind giving me more details about your use-case, perhaps I can improve Beast's interfaces to support it. Or suggest better ways of accomplishing the same result.

---

## Comment 15

> Username: georgi-d  
> Created at: 2016-11-11 12:05:46 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259943950  

I have two use cases:  
  
1) High performance HTTP reverse proxy which accepts HTTP requests, parses the headers and based on the path of the request creates a new HTTP to a backend server forwarding the body of the client request to the backend server and the response from the server back to the client asynchronously.  The proxy should have fixed memory footprint per connection so it should be able to use a single fixed size buffer per connection and not read the whole message body before forwarding it.  
  
2) A high performance HTTP service which for each HTTP request calls a handler (after parsing the headers) passing it a Request and Response objects.   
- The Request should have accessors for the request headers, verb and path and should also have async_read() for reading the request body. The async_read() call should return system::eof when the end of the body is reached.  
- The Response  object should have setters for the result code and headers and it should also provide async_write() for writing the response body. The Response should have a finish() method for indicating the end of the response. When finish() is called the underlying stream should be returned to the server for waiting and handling a consecutive request from the peer.  
  
Request::async_read() and Response::async_write should be callable multiple times for processing a client request.   
  
I plan on using 2) for implementing 1) and as a transport for a generic REST/RPC framework. The REST/RPC framework allows based on an IDL definition to have client side stubs and server side REST and/or JsonRPC exposed implementation.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2016-11-11 12:55:16 UTC  
> Updated at: 2016-11-11 14:21:05 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259951513  

This is great, thanks. It sounds like Beast has good interfaces for handling the write side, but we might need to do a little more work on reading. Some questions:  
- How will you handle trailing headers?  
- How will you handle Expect: 100-continue?  
- How do you feel about the current division between `message` and `message_header` (which will be renamed to just `header`), and the routines to write them?

---

## Comment 17

> Username: georgi-d  
> Created at: 2016-11-11 15:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-259982504  

> - How are trailing headers handled?  
- I have not considered trailing headers up to now but I think it would be fine if they appear in the headers container of the message after async_read() returns eof(). I would be fine if they are not supported at all as well.  
  
> How is Expect: 100-continue handled?  
- on the server side: I think it would be better if handling is explicit. When the handler is called the the code should explicitly check for is_continue_required(request), do any additional checks and do a async_write_response_continue() before doing any async_read() calls.  
- on the client side I think it should be handled explicitly as well. If the client adds an Expect: 100-continue header then it should do an explicit async_read_continue() before doing any writes to the request body. The the client should do another async_parse/async_read which should fill any additional headers. Then the response body could be read.  
  
>  How do you feel about the current division between message and message_header (which will be renamed to just header), and the routines to write them  
- I have not looked too deep into them but I like the separation in general and the presence of the headers_parser which allows to parse the headers, do some handling and then move to parsing the body. On the write side I have not looked much yet. I am still working on the read side. I would like to be able to write the headers separately and then write the body.

---

## Comment 18

> Username: georgi-d  
> Created at: 2016-11-11 18:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260021840  

I have created a draft change which adds CompletionCondition to async_parse():  
  
https://github.com/georgi-d/Beast/commit/c14400711f63b6b1010cd447ace0fce11fb0ada5  
For now I have only tested that it compiles. Will write some unit tests next.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2016-11-11 18:35:07 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260024115  

I'm not sure that a completion condition is the best way to solve this problem. I need more time to think about it. I believe we can do better.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2016-11-13 23:18:05 UTC  
> Updated at: 2016-11-13 23:23:17 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260220904  

@georgi-d I've been thinking a lot about this, and I am starting to think that the "stateless" model of the interface is not the right approach. What I mean is that free functions don't encapsulate enough state for us to do useful things. Now I am thinking about a new approach, similar to what is done with websocket:  
  
```  
namespace beast {  
namespace http {  
  
template<bool isServer, class NextLayer>  
class stream_v1  
{  
    streambuf rb_;  
    NextLayer next_layer_;  
    basic_parser_v1 parser_;  
  
public:  
    template<class... Args>  
    stream_v1(Args&&... args)  
        : next_layer_(std::forward<Args>(args)...)  
    {  
    }  
  
    // Read the header asynchronously  
    template<class Fields, class ReadHandler>  
    void  
    async_read(header<! isServer, Fields>& h, ReadHandler&& handler);  
  
    // Read some of the body asynchronously  
    template<class MutableBufferSequence, WriteHandler>  
    std::size_t  
    async_read_some(MutableBufferSequence const& buffer, WriteHandler&& handler);  
  
    // Write the header asynchronously  
    template<class Fields, class WriteHandler>  
    void  
    async_write(header<! isServer, Fields> const& h, WriteHandler&& handler);  
  
    // Write some of the body asynchronously  
    template<class MutableBufferSequence, class WriteHandler>  
    std::size_t  
    async_write(MutableBufferSequence const& buffer, WriteHandler&& handler);  
};  
  
} // http  
} // beast  
  
// typical use:  
boost::asio::io_service ios;  
beast::http::stream<boost::asio::ip::tcp::socket> stream_v1{ios};  
```  
  
This allows us to make the parsing state a member of the class, which persists across member function calls. We would do away with the free functions like `http::async_read` and replace them with member functions. The parser would always exist, and always have the correct state. This allows us to handle the body differently if we want. It will take me more time to sketch out how this might look in practice. It will require significant modifications to the parser.  
  
We can probably keep the free functions for simple use-cases though.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2016-11-14 00:06:22 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260223580  

How do you feel about this:  
  
`do_process` and `do_connect` are user-defined:  
  
```  
template<class AsyncServerStream, class AsyncClientStream>  
void reverse_proxy(AsyncClientStream& si, yield_context yield)  
{  
    using namespace boost::asio;  
    stream_v1<true, AsyncClientStream> ssi{si};  
    // read request header from client  
    request_header req;  
    ssi.async_read(req, yield);  
    // apply request transformation  
    do_process(req);  
    // establish stream to backend server  
    stream_v1<false, AsyncServerStream> sso{true, do_connect(req, yield)};  
    // write transformed request to server  
    sso.async_write(req, yield);  
    // transfer request body to server  
    char buf[4096];  
    {  
        error_code ec;  
        for(;;)  
        {  
            auto const bytes_transferred =  
                ssi.async_read_some(buffer(buf, sizeof(buf)), yield[ec]);  
            if(ec == error::end_of_message)  
                break;  
            if(ec)  
                throw system_error{ec};  
            sso.async_write(buffer(buf, bytes_transferred), yield);  
        }  
    }  
    // read response header from server  
    response_header res;  
    sso.async_read(res, yield);  
    // apply response transformation  
    do_process(res);  
    // transfer response body to client  
    ssi.async_write(res, yield);  
    {  
        error_code ec;  
        for(;;)  
        {  
            auto const bytes_transferred =  
                sso.async_read_some(buffer(buf, sizeof(buf)), yield[ec]);  
            if(ec == error::end_of_message)  
                break;  
            if(ec)  
                throw system_error{ec};  
            ssi.async_write(buffer(buf, bytes_transferred), yield);  
        }  
    }  
}  
```

---

## Comment 22

> Username: georgi-d  
> Created at: 2016-11-14 11:44:00 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260314912  

I like this idea. I just have a few notes:  
- we should be able to differentiate when an async_read_some returns eof() because it has reached the end of the body or when it has reached the end of the stream (the socked was closed). This is important to be able to handle multiple requests on the same socket  
- after the last async_read_some returns end-of-message it should be able to issue another read for the headers to process the next request on the stream.  This should work for pipelined requests as well.  
- I would make the parser state a public class/struct  
- I would make the asyc_read/write(stream, stream_buf, parser, parser_state, headers) and async_read_some/write(stream, stream_buf, parser, parser_state, buffer) global public methods and then wire stream_v1 to just call them. This will make the implementation more flexible because if needed the state can be managed manually by the application and still achieve the functionality of stream_v1.  
- for the trailing headers case it might be needed to pass the headers container to async_read_some as well so it can be updated. Alternatively it could be required to call async_read(headers<trailers>) after async_read_some returnes end_of_body() to consume the trailers.

---

## Comment 23

> Username: vinniefalco  
> Created at: 2016-11-14 11:55:00 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260316872  

- If the socket was closed, you would get `error::end_of_message` first, and then on the next call to `async_read` you would immediately get `eof`.  
- Right, it is the intention that `reverse_proxy` is called in a loop  
  
The problem again is the chunked encoding and trailers. Does `async_read_some` remove the chunked-encoding for you?

---

## Comment 24

> Username: georgi-d  
> Created at: 2016-11-14 13:43:40 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260337440  

I believe that when reading from the stream_v1 the chunk encoding should be removed and only the body data should be available in the buffer.

---

## Comment 25

> Username: vinniefalco  
> Created at: 2016-11-14 13:46:12 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260337986  

> I believe that when reading from the stream_v1 the chunk encoding should be removed and only the body data should be available in the buffer.  
  
But then we don't know the content length, and the reverse proxy can't forward the body. Look in my code example, the `request_header` is passed to the upstream connection. If that header comes in with Transfer-Encoding: chunked, then we have to remove that transfer encoding and replace it with Content-Length. But we don't know the content length, because you don't want to read the whole body into memory first.

---

## Comment 26

> Username: georgi-d  
> Created at: 2016-11-14 14:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260352392  

When the request is chunked the reverse proxy should just do its own chunk encoding on the write side. We could add a separate async_read_some_raw() method if we consider re-encoding on the write side a performance bottleneck. The normal functionality should be to remove the chunk encoding on async_read_some() since only a proxy implementation would benefit from not removing the chunk encoding. All normal HTTP server use cases would need the chunk encoding to be stripped.

---

## Comment 27

> Username: vinniefalco  
> Created at: 2016-11-14 15:09:32 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260360243  

So, lets say the client sends a one megabyte body broken up into four chunks of 256KB each. Now we forward that to the proxy, but we are using a 4096-byte buffer so the upstream is going to get a body with 256 chunks? That increases the overhead for no good reason, is that okay?

---

## Comment 28

> Username: georgi-d  
> Created at: 2016-11-14 15:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260369811  

If it uses the default behavior I think that would be expected and reasonable. On the other hand now that I think about it the switch whether to strip the chunk encoding or not should be a parameter of the parser and not async_read_some(). So the reverse proxy should set a compile time (template) parameter of the parser that it does not need stripping of the chunk encoding. Then the original chunks can be preserved.

---

## Comment 29

> Username: vinniefalco  
> Created at: 2016-11-14 15:57:28 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260375039  

Or, how about the parser gives information about the body in the `on_body` callback for example:  
  
```  
struct chunk_info  
{  
    std::size_t chunk_size; // total size of this chunk, or 0 if not chunk-encoded  
};  
  
void  
on_body(chunk_info& ci, boost::string_ref const& data);  
```

---

## Comment 30

> Username: georgi-d  
> Created at: 2016-11-14 16:10:21 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260379077  

I do not like this approach, because it first complicates the API without much benefit and it also would require adding more state on the write side to be able to pass the chunk size beforehand and then fill the chunk with multiple writes (complex and error prone,) or it would require for the proxy to adjust its buffer size to the client's chunk size. This would expose a remote exploit hole because a a malicious client can set a huge chunk size and crash the server with out of memory.   
  
I think the API should be to either chunk encode on the write side to your buffer size or explicitly configure your parser to give you undecoded chunk data when reading and then raw write. The latter is rare and special enough to require a different type (similar to the request/response messages are differentiated) and not a runtime parameter to the parser.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2016-11-14 16:18:35 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260381483  

So, if the parser is set to pass-through chunked encoding, it still has to parse the data so it knows where the end of the message is. What do we do about the trailers in that case?

---

## Comment 32

> Username: georgi-d  
> Created at: 2016-11-14 16:27:20 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260384239  

Yes, it will still have to parse the data to know where the end of the message is.  
  
For the trailing headers I think the proxy will have to handle them explicitly. It will have to call async_read(trailers) after receiving error::end_of_message if has_trailers(headers) has returned true. Then it would have to write them with async_write(trailers).

---

## Comment 33

> Username: vinniefalco  
> Created at: 2016-11-14 16:28:39 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260384604  

So you want to read the trailers into a new object? We can't use `request_header` or `response_header` since those have extra information. Are you thinking that we should read the trailers directly into a `basic_fields` (currently called `basic_headers`)?

---

## Comment 34

> Username: georgi-d  
> Created at: 2016-11-14 16:46:42 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260389917  

According to the spec all trailers should be first listed in the Trailer: header. So there should be a get_trailers(headers) helper which would setup a structure with the expected trailers. Then we should be able to fill the trailers into basic_fields object and validate that all the promised trailers are present. This would mean we would need async_read(expected_trailers, fields).

---

## Comment 35

> Username: georgi-d  
> Created at: 2016-11-14 16:52:15 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260391611  

I think it might be worth having error::expect_trailers()  or similar error code which is returned after the body has been read and the parser knows that trailers are expected so the application can switch to async_read(trailers).

---

## Comment 36

> Username: vinniefalco  
> Created at: 2016-11-14 16:58:49 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260393690  

This is all starting to sound very messy!

---

## Comment 37

> Username: georgi-d  
> Created at: 2016-11-14 17:02:33 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260394818  

It is possible to make due without an extra error code. This was just a fruit for thought. I would be fine without it and the application having to check against the headers or the parser. I saw that the parser has a state for expecting trailers.

---

## Comment 38

> Username: georgi-d  
> Created at: 2016-11-14 17:22:10 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260400193  

The other general approach is to just make async_read_some receive headers object as parameter so the parser can fill the trailers in if/when they arrive.  The error::end_of_message() would be returned after the full body and the trailer has been consumed. This will simplify the state machine the application. For the reverse proxy we would need to be able to write the trailers after writing out the body.

---

## Comment 39

> Username: georgi-d  
> Created at: 2016-11-16 15:02:13 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-260967743  

It appears that picohttp parser either ignores the trailiers or requires a separate parse_headers call:  
  
```  
phr_decode_chunked(...)  
...  
/* set consume_trailer to 1 to discard the trailing header, or the application  
 * should call phr_parse_headers to parse the trailing header */  
```

---

## Comment 40

> Username: vinniefalco  
> Created at: 2016-11-17 20:12:10 UTC  
> Updated at: 2016-11-17 21:38:06 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-261356144  

@georgi-d Here's an improved version of a function that helps a proxy relay messages. This one handles the trailers. It makes considerable changes to the parser interface. I believe these changes will allow us to improve performance significantly (it borrows techniques from picohttpparser):  
  
```  
/// Efficiently relay one HTTP message between two peers  
template<  
    bool isRequest,  
    class InAsyncStream,  
    class OutAsyncStream,  
    class MessageTransformation>  
void  
relay_message(  
    InAsyncStream& si,  
    OutAsyncStream& so,  
    error_code& ec,  
    yield_context yield,  
    MessageTransformation const& transform)  
{  
    using namespace beast::http;  
    parser_v1<isRequest> p;  
  
    // read the incoming message headers  
    for(;;)  
    {  
        // TODO skip async_read_some if parser already has the next set of headers  
        auto const bytes_transferred =  
            si.async_read_some(p.prepare(), yield[ec]);  
        if(ec)  
            return;  
        p.commit_header(bytes_transferred, ec);  
        if(ec == parse_error::need_more)  
        {  
            ec = {};  
            continue;  
        }  
        if(ec)  
            return;  
        break;  
    }  
  
    // Create a new message by transforming the input message  
    // At minimum this will remove Content-Length and apply Transfer-Encoding: chunked  
    auto req = transform(p.get(), ec);  
    if(ec)  
        return;  
  
    // send the header  
    async_write(so, req, yield[ec]);  
    if(ec)  
        return;  
  
    for(;;)  
    {  
        // TODO skip async_read_some if we already have body data  
        // Read the next part of the body  
        auto const bytes_transferred =  
            si.async_read_some(p.prepare(), yield[ec]);  
        if(ec)  
            return;  
        p.commit_body(bytes_transferred, ec);  
        if(ec == parse_error::end_of_message)  
            break;  
        if(ec)  
            return;  
  
        // Forward this part of the body  
        // p.body() removes any chunk encoding  
        //  
        if(buffer_size(p.body()) > 0)  
        {  
            async_write(so, chunk_encode(p.body()), yield[ec]);  
            if(ec)  
                return;  
        }  
    }  
  
    // Copy promised trailer fields from incoming request  
    // that are not already present in the outgoing response:  
    //  
    for(auto field : token_list{req.fields["Trailer"]})  
        if(! req.contains(field))  
            req.insert(field, p.get().fields[field]);  
  
    // Send the final chunk, including any promised trailer fields  
    //  
    streambuf sb;  
    write_final_chunk(sb, req);  
    async_write(so, sb.data(), yield[ec]);  
    if(ec)  
        return;  
}  
```

---

## Comment 41

> Username: georgi-d  
> Created at: 2016-11-17 20:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-261361474  

It looks good to me. I have just a couple of comments:  
  
```  
// Forward this part of the body  
        // p.body() removes any chunk encoding  
        //  
        async_write(so, chunk_encode(p.body()), yield[ec]);  
        if(ec)  
            return;  
    }  
   // clear the body from the buffer  
```  
  
I think the body buffer should be cleared explicitly here before looping and reading the next part.  
  
```  
streambuf sb;  
    write_final_chunk(sb, req1);  
    async_write(so, sb.data(), yield[ec]);  
    if(ec)  
        return;  
}  
```  
  
Can we avoid the use of `streambuf` here and make write_final_chunk() or add another method make_final_chunk that returns a ConstBufferSequence the same way chunk_encode() does? This is not a blocker just a small optimization.

---

## Comment 42

> Username: vinniefalco  
> Created at: 2016-11-17 20:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-261362402  

> I think the body buffer should be cleared explicitly here before looping and reading the next part.  
  
The return value of `p.body()` is invalidated by a subsequent call to `prepare()`. There's no need to "clear" it, because the storage belongs to the parser. It manages an internal buffer. Note that this is very different from the current implementation, which requires a caller-managed **DynamicBuffer** object.  
  
> Can we avoid the use of streambuf here   
  
You can only avoid `streambuf` if there are no trailers. This isn't any different from the internal `streambuf` used when writing the header via `async_write(so, req1, yield[ec]);`.  
  
This new code solves a few problems from the previous version so I think this is a big step forward. But I am still not completely happy with it yet. It needs a little more polishing I think.

---

## Comment 43

> Username: georgi-d  
> Created at: 2016-11-17 21:01:49 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-261368317  

I agree. I have a question. What happens to the bytes in the buffer that are part of a consecutive message and are not consumed by the parser? These should be available to a second call to parse headers. I liked the fact that the application could own the buffer it is read into from the socket and the parser was indicating exactly how many were consumed. With the current implementation I feel only the same parser can be used for the next message unless there is a call for the parser to release the buffer and move return it.  
Why is it needed for the parse to own the buffer? I prefer if the application had more control over the buffer of the body. For the proxy it is not needed but for a normal processing of the body it might be better if it is possible to keep a few chunks until a certain marker is reached and then process and drop them together.

---

## Comment 44

> Username: vinniefalco  
> Created at: 2016-11-17 21:44:58 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-261379415  

> What happens to the bytes in the buffer that are part of a consecutive message and are not consumed by the parser?  
  
I was thinking they could be used in the next call to parse  
  
> I liked the fact that the application could own the buffer  
  
If the application owns the buffer, then it forces the library to do an additional allocate/copy for the header fields. If the parser owns the buffer, we can do some significant optimizations. Take a look at how picohttpparser does it:  
https://github.com/h2o/picohttpparser

---

## Comment 45

> Username: vinniefalco  
> Created at: 2016-11-17 22:27:55 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-261390020  

I see what you mean though, for the case when there is no chunk-encoding, it makes more sense for the application to be in control of the buffer. There is still more design work to do...

---

## Comment 46

> Username: vinniefalco  
> Created at: 2016-11-23 17:26:34 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-262579764  

Further improvements. `parse_buffer` is a new type:  
```  
template<class SyncReadStream,  
    bool isRequest, class Body, class Fields>  
void  
read(SyncReadStream& stream, parse_buffer& buffer,  
    message<isRequest, Body, Fields>& msg, error_code& ec)  
{  
    using boost::asio::buffer_copy;  
    using boost::asio::buffer_size;  
  
    new_parser_v1<isRequest, Body, Fields> p;  
  
    // Read and parse header  
    for(;;)  
    {  
        p.write(buffer, ec);  
        if(! ec)  
            break;  
        if(ec != error::need_more)  
            return;  
        ec = {};  
        auto const bytes_transferred =  
            stream.read_some(buffer, ec);  
        if(ec)  
            return;  
        buffer.commit(bytes_transferred);  
    }  
  
    /* At this point one of the following applies:  
        1. No body is expected:  
            - 100 level response  
            - Upgrade request  
            - Reading just the header  
            - Caller said so  
        2. Content-Length is known (no chunked)  
        3. Transfer-Encoding: chunked (no Content-Length)  
        4. No Content-Length, no chunked  
    */  
    // Read and parse body  
    typename Body::reader r{p.get(), p.content_length()};  
    if(p.content_length())  
    {  
        auto remain = *p.content_length();  
        {  
            // Copy leftovers in buffer  
            auto const n = buffer_size(p.body(buffer));  
            if(n > 0)  
            {  
                buffer_copy(r.prepare(n), p.body(buffer));  
                remain -= n;  
            }  
        }  
        while(remain > 0)  
        {  
            auto const buffers = r.prepare(remain, ec);  
            if(ec)  
                return;  
            auto const bytes_transferred =  
                stream.read_some(buffers, ec);  
            if(ec)  
                return;  
            r.commit(bytes_transferred, ec);  
            if(ec)  
                return;  
            remain -= bytes_transferred;  
            if(remain == 0)  
                break;  
        }  
    }  
    else if(p.chunked())  
    {  
        for(;;)  
        {  
            for(;;)  
            {  
                p.write(buffer, ec);  
                if(! ec)  
                    break;  
                if(ec != error::need_more)  
                    return;  
                ec = {};  
                auto const bytes_transferred =  
                    stream.read_some(buffer, ec);  
                if(ec)  
                    return;  
                buffer.commit(bytes_transferred);  
            }  
            if(p.complete())  
                break;  
            remain = p.chunk_size();  
            // Copy leftovers in buffer  
            auto const n = buffer_size(p.body(buffer));  
            if(n > 0)  
            {  
                buffer_copy(r.prepare(n), p.body(buffer));  
                remain -= n;  
            }  
            while(remain > 0)  
            {  
                auto const buffers = r.prepare(remain, ec);  
                if(ec)  
                    return;  
                auto const bytes_transferred =  
                    stream.read_some(buffers, ec);  
                if(ec)  
                    return;  
                r.commit(bytes_transferred, ec);  
                if(ec)  
                    return;  
                remain -= bytes_transferred;  
                if(remain == 0)  
                    break;  
            }  
        }  
    }  
    else  
    {  
        buffer_copy(r.prepare(buffer_size(  
            p.body(buffer))), p.body(buffer));  
        remain -= n;  
        for(;;)  
        {  
            auto const buffers =  
                r.prepare(r.read_size(), ec);  
            if(ec)  
                return;  
            auto const bytes_transferred =  
                stream.read_some(buffers, ec);  
            if(ec == boost::asio::error::eof)  
            {  
                // caller sees eof on the next read  
                ec = {};  
                break;  
            }  
            if(ec)  
                return;  
            r.commit(bytes_transferred, ec);  
            if(ec)  
                return;  
        }  
    }  
    r.finish(ec);  
    if(ec)  
        return;  
}  
```

---

## Comment 47

> Username: vinniefalco  
> Created at: 2016-11-23 23:10:22 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-262652280  

Further simplification:  
```  
template<class SyncReadStream,  
    bool isRequest, class Body, class Fields>  
void  
read(SyncReadStream& stream, parse_buffer& buffer,  
    message<isRequest, Body, Fields>& msg, error_code& ec)  
{  
    using boost::asio::buffer_copy;  
    using boost::asio::buffer_size;  
  
    new_parser_v1<isRequest, Body, Fields> p;  
  
    // Read and parse header  
    for(;;)  
    {  
        p.write(buffer, ec);  
        if(! ec)  
            break;  
        if(ec != error::need_more)  
            return;  
        ec = {};  
        auto const bytes_transferred =  
            stream.read_some(buffer, ec);  
        if(ec)  
            return;  
        buffer.commit(bytes_transferred);  
    }  
        
    typename Body::reader r{p.get(), p.content_length()};  
  
    // Read and parse body  
    while(! p.complete())  
    {  
        // maybe read chunk delimiter  
        for(;;)  
        {  
            p.write(buffer, ec);  
            if(! ec)  
                break;  
            if(ec != error::need_more)  
                return;  
            ec = {};  
            auto const bytes_transferred =  
                stream.read_some(buffer, ec);  
            if(ec)  
                return;  
            buffer.commit(bytes_transferred);  
        }  
        // copy body bytes in buffer  
        p.write_body(r, buffer, ec);  
        if(ec)  
            return;  
        auto const read_size = p.read_size(r, buffer);  
        auto const buffers = r.prepare(read_size, ec);  
        if(ec)  
            return;  
        auto const bytes_transferred =  
            stream.read_some(buffers, ec);  
        if(ec == boost::asio::error::eof)  
        {  
            ec = {};  
            p.write_eof(ec);  
        }  
        if(ec)  
            return;  
        r.commit(bytes_transferred, ec);  
        if(ec)  
            return;  
        p.consume_body(bytes_transferred);  
    }  
    r.finish(ec);  
    if(ec)  
        return;  
}  
```

---

## Comment 48

> Username: georgi-d  
> Created at: 2016-11-24 10:44:46 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-262746000  

If I understand this correctly it is still trying to read the whole message at once.  I do not see from here how one can control how much of the body/headers are read in one call.  
  
What concept/interface do parse_buffer and Body have?

---

## Comment 49

> Username: georgi-d  
> Created at: 2016-11-25 23:18:00 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263031588  

I feel the state machine is too complicated and the parser interface currently does not give enough control over the body buffer.   
The concept of Body also feels a bit over complicated.  
  
After some consideration I thnk the parser should be much simpler and have members only for its own internal state.  
  
The parser and the methods to read the full message should look like this:  
  
```  
template <bool IsRequest, bool ChunkDecodeBody = true>  
class parser_v1 {  
  
   /*  
    Parses an HTTP message filling the fields and the body buffer  
  
    @param buffer [in] raw http buffer to read from  
    @param fields [in/out] container to add the headers, trailers and verb/status  
    @param body [out] buffer to store any parsed body data  
    @param ec [out] error::need_more if more input is needed to complete the message  
                    error::have_more if the body buffer is full and there is more data in the input  
                    parse error   
  
    @return pair containing bytes_consumed and body_bytes produced  
   */  
   template <class ConstBufferSequence, Fields, MutableBufferSequene>  
   pair<size_t, size_t>  
   write(const ConstBufferSequence& buffer, Fields& fields, MutableBufferSequene& body, error_code& ec);  
  
  
   /*  
    Parses an HTTP message headers only filling the fields  
  
    @param buffer [in] raw http buffer to read from  
    @param fields [in/out] container to add the headers, trailers and verb/status  
    @param body [out] buffer to store any parsed body data  
    @param ec [out] error code if error is encountered or error::need_more if the end of the headers have not been reached  
  
    @return pair containing bytes_consumed and 0  
   */  
   template <class ConstBufferSequence, class Fields>  
   pair<size_t, size_t>   
   write(const ConstBufferSequence& buffer, Fields& fields, error_code& ec);  
  
   /*  
   Returns whether the complete message has been parsed  
   */  
   bool complete();  
};  
  
  
  
/*  
Read and parse the HTTP headers  
*/  
template<class SyncStream, class Parser, class ReadDynamicBufferSequence, class Fields>  
void  
read_headers(SyncStream& stream, Parser& parser, ReadDynamicBufferSequence& read_buf, Fields& fields, error_code& ec)  
{  
   for (auto bp = parser.write(read_buf.data(), fields, ec), readbuf.consume(bp.first);  
        !parser.complete() && ec == error::need_more();  
        bt = parser.write(read_buf.data(), fields, ec), readbuf.consume(bp.first))  
   {  
      auto const rs = read_size(read_buf);  
      ec = {};  
      const auto br = stream.read_some(read_buf.prepare(rs), ec);  
      read_buf.commit(br);  
      if (ec)  
      {  
         return;  
      }  
   }  
}  
  
  
/*  
Read a full HTTP message filling the body into a stream_buf  
TODO: Add support for CompletionCondition  
*/  
template<class SyncStream, class Parser, class ReadDynamicBufferSequence, class Fields, class BodyDynamicBufferSequence>  
void  
read(SyncStream& stream, Parser& parser, ReadDynamicBufferSequence& read_buf, BodyDynamicBufferSequence& body, error_code& ec)  
{  
   read_headers(stream, parser, read_buf, fields, ec);  
  
   if (ec)  
   {  
      return;  
   }  
  
   if (continue_required(fields))  
   {  
      write_continue(stream);  
   }  
  
   const auto ws = prepare_size(body);  
  
   for (auto bp = parser.write(read_buf.data(), fields, body.prepare(ws) ec), readbuf.consume(bp.first), body.commit(bp.second);  
        !parser.complete() && (ec == error::need_more() || ec == error::have_more());  
        bt = parser.write(read_buf.data(), fields, ec), readbuf.consume(bp.first), body.commit(bp.second))  
   {  
      if (ec == error::need_more())  
      {  
         auto const rs = prepare_size(read_buf);  
         ec = {};  
         const auto br = stream.read_some(read_buf.prepare(rs), ec);  
         read_buf.commit(br);  
         if (ec)  
         {  
            return;  
         }  
      }  
   }  
}  
```  
  
On top of the simple parser interface it is very easy to expose a http_stream_v1 implementation which holds reference to (parser, fields, stream_buf)  
 and allows to read the body into a MutableBuffer. The http stram would return eof() when the end of the message is reached.  
  
This design also allows operation only with static buffers if needed.

---

## Comment 50

> Username: vinniefalco  
> Created at: 2016-11-25 23:36:11 UTC  
> Updated at: 2016-11-25 23:36:48 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263032522  

I want to make the parser operate on a linear buffer instead of a **BufferSequence**. There are significant optimizations that can be performed if the entire header is in a single piece of memory. There's a trade-off here, because you have to either realloc if you don't have enough of the header, or you need to pre-allocate a larger than necessary buffer. However, I believe that its worth it. **picohttpparser** (https://github.com/h2o/picohttpparser) outperforms the Node.JS parser that Beast is modeled after, by 1000% (!).  
  
Having the whole header in a single buffer also allows for better parsing of the URI in request messages. I can add a parser to break down the URI into its scheme, authority, userinfo, path, and query parameters. This is a significant improvement that users have requested.  
  
A single buffer also allows efficient handling of continuation lines (they can be combined in place). And it allows for better treatment of chunk extensions (currently unimplemented in Beast).  
  
I appreciate that you have taken the time to propose a design for the parser. One thing I notice in your interface, it requires buffer copying to process the body. In the model I am trying to build, the read function transfers data directly from the socket into the container used to store the body, with no intermediate buffer sequence (except for a small constant number of leftover bytes from the linear buffer which must be copied).  
  
I agree that the current **Reader** concept is likely overcomplicated. However, it still needs to exist to allow callers to customize the type of the `message::body` member (and still be able to read it in).  
  
As for the complexity of the parser I am trying to build, I agree that it is not exactly simple. Hopefully I can simplify it some. But HTTP is not a simple protocol  so I think there are limits to how simple it can be, while also allowing a maximally performant implementation. I'm okay with some complexity as long as it enables all the use cases that people might want. Beast intends to be a low level library (for HTTP). The design goals are flexibility, performance, and simplicity in that order.  
  
I'm still working on a prototype of the things we've talked about, this is where I'm at:  
https://github.com/vinniefalco/Beast/blob/http/test/http/new_parser.cpp  
It is still a work in progress.

---

## Comment 51

> Username: georgi-d  
> Created at: 2016-11-28 01:26:42 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263163796  

I understand the optimization opportunities when using continuous buffer for the headers.  
The design I propose can very easily be modified to work with continuous buffers by changing all MutableBufferSequene to asio::mutable_buffer and ConstBufferSequence to asio::const_buffer.  
  
If the parser needs to be able to modify the buffer so it can combine headers then it could require a mutable_buffer instead of a const_buffer.  
  
  
The most important difference in the design is that the parser does not own (have a member) the message, body or headers.  
They are provided as output parameters which makes them easily customizable. In this case I do not see a reason for the Body and Reader concepts in the parser. They could exist for the Message class if a convenience methods are exposed for packing the full HTTP message in one structure.  
  
I do not agree that the design requires an extra copy, and actually if the parser do not own the message and the body the parser can be zero copy on the body.  
It can just return a non owning asio::const_buffer to the body. If there is a chunk border in the buffer it would just return the pieces one by one with error::have_more.  
  
It would look like this:  
  
```  
  
template <bool IsRequest, bool ChunkDecodeBody = true>  
class parser_v1 {  
  
   /*  
    Parses an HTTP message filling the fields and the body buffer  
  
    @param buffer [in] raw http buffer to read from  
    @param fields [in/out] container to add the headers, trailers and verb/status  
    @param body [out] buffer to store any parsed body data  
    @param ec [out] error::need_more if more input is needed to complete the message  
                    error::have_more if there is more body bytes in the same buffer. The method should be called again to retrieve the next body bytes.  
                    parse error   
  
    @return pair containing bytes_consumed and body_bytes const_buffer within the buffer  
   */  
   template <Fields>  
   pair<size_t, asio::const_buffer>  
   write(const asio::mutable_buffer& buffer, Fields& fields, asio::mutable_buffer& body, error_code& ec);  
  
  
   /*  
    Parses an HTTP message headers only flling the fields  
  
    @param buffer [in] raw http buffer to read from  
    @param fields [in/out] container to add the headers, trailers and verb/status  
    @param body [out] buffer to store any parsed body data  
    @param ec [out] error code if error is encountered or error::need_more && result.first = 0 if the end of the headers have not been reached  
  
    @return bytes_consumed  
   */  
   template <class Fields>  
   size_t  
   write_headers(const asio::mutable_buffer& buffer, Fields& fields, error_code& ec);  
  
   /*  
   Returns whether the complete message has been parsed  
   */  
   bool complete();  
};  
  
  
  
/*  
Read and parse the HTTP headers  
*/  
template<class SyncStream, class Parser, class ContinuousDynamicBuffer, class Fields>  
void  
read_headers(SyncStream& stream, Parser& parser, ContinuousDynamicBuffer& read_buf, Fields& fields, error_code& ec)  
{  
   for (auto bc = parser.write_headers(read_buf.data(), fields, ec), read_buf.consume(bc);  
        !parser.complete() && ec == error::need_more();  
        bt = parser.write_headers(read_buf.data(), fields, ec), read_buf.consume(bc))  
   {  
      auto const rs = read_size(read_buf);  
      ec = {};  
      const auto br = stream.read_some(read_buf.prepare(rs), ec);  
      read_buf.commit(br);  
      if (ec)  
      {  
         return;  
      }  
   }  
}  
  
  
/*  
Read a full HTTP message filling the body into a stream_buf  
TODO: Add support for CompletionCondition  
*/  
template<class SyncStream, class Parser, class ContinuousDynamicBuffer, class Fields, class DynamicBufferSequence>  
void  
read(SyncStream& stream, Parser& parser, ContinuousDynamicBuffer& read_buf, DynamicBufferSequence& body, error_code& ec)  
{  
   read_headers(stream, parser, read_buf, fields, ec);  
  
   if (ec)  
   {  
      return;  
   }  
  
   if (continue_required(fields))  
   {  
      write_continue(stream);  
   }  
  
   for (auto bp = parser.write(read_buf.data(), fields, ec), read_buf.consume(bp.first), body.commit(asio::buffer_copy(body.prepare(asio::buffer_size(bp.second)), bp.second));  
        !parser.complete() && (ec == error::need_more() || ec == error::have_more());  
        bt = parser.write(read_buf.data(), fields, ec), readbuf.consume(bp.first), body.commit(asio::buffer_copy(body.prepare(asio::buffer_size(bp.second)), bp.second))  
   {  
      if (ec == error::need_more())  
      {  
         auto const rs = prepare_size(read_buf);  
         ec = {};  
         const auto br = stream.read_some(read_buf.prepare(rs), ec);  
         read_buf.commit(br);  
         if (ec)  
         {  
            return;  
         }  
      }  
   }  
}  
  
```

---

## Comment 52

> Username: vinniefalco  
> Created at: 2016-11-28 01:49:08 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263165865  

There is something I don't understand here. You have this member function:  
```  
template <Fields>  
std::pair<size_t, asio::const_buffer>  
parser_v1::write(  
    const asio::mutable_buffer& buffer,  
    Fields& fields,  
    asio::mutable_buffer& body,  
    error_code& ec);  
```  
  
`buffer` contains raw HTTP data. For example, it might contain the entire header, followed by some of a chunk-encoded body. The `write` function above applies octets in the buffer to the header, and then returns any octets belonging to the body through the `body` argument, which is an output parameter.  
  
At this point the caller has to copy the bytes in `body` over to the container used to represent the body in the message - this is the additional buffer copy that I want to avoid. You said there is no extra buffer copy but yet, here it is.  
  
In your example `read` function, the extra buffer copy happens in this statement:  
```  
    asio::buffer_copy(body.prepare(asio::buffer_size(bp.second)), bp.second)  
```  
  
Compare with the latest version of the prototype I am working on, which reads most of the body directly into the buffer provided by the reader:  
https://github.com/vinniefalco/Beast/blob/http/test/http/new_parser.cpp#L1204

---

## Comment 53

> Username: georgi-d  
> Created at: 2016-11-28 08:30:01 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263210776  

The `body` parameter is a leftover from the previous version. The correct signature is:  
```  
template <Fields>  
 pair<size_t, asio::const_buffer>  
 write(const asio::mutable_buffer& buffer,  
           Fields& fields,  
           error_code& ec);  
```  
  
The parse does not make a copy in a separate body buffer it just returns a const_buffer where the body is in the read buffer.  
  
The copy in the `read` method is optional and up to the user. It is there currently because `read` is a convenience method that copies the body in another user provided buffer.  
Any other combinations are also possible:  
- process the chunks directly as the parser returns them. [zero copy]  
- have a `std::vector<asio::const_buffer>` as a parameter which is filled with all the chunk const_buffers while keeping the actual data in the read buffer extending the read buffer [zero copy unless there is a copy due to reallocation of the read buffer]

---

## Comment 54

> Username: vinniefalco  
> Created at: 2016-11-28 11:23:17 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263247220  

The interface you propose works well for the case where you want to to implement a relaying proxy but requires a buffer copy for the case where the caller wants to fill out the `body` field of a `message`. Given:  
```  
template <Fields>  
 pair<size_t, asio::const_buffer>  
 write(const asio::mutable_buffer& buffer,  
           Fields& fields,  
           error_code& ec);  
```  
  
How does the body data get into the `body` field of `message<true, string_body, fields>`? It has to be copied.

---

## Comment 55

> Username: georgi-d  
> Created at: 2016-11-28 14:07:33 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263279427  

I would disagree it works only in the proxy case. It works well in all cases where you want a object for the (headers, verb, path, status code, etc) and a separate async stream object for the body which you want to read piece by piece. This is almost all the cases I have for an HTTP library. Proxy and an REST application server.  
  
Looking at the new_parser it is not completely zero copy there is a copy from the parse buffer to the body buffer around the chunk headers:  
  
https://github.com/vinniefalco/Beast/blob/http/test/http/new_parser.cpp#L1205 and L1206:  
  
```  
// copy body bytes in buffer  
p.write_body(r, buffer, ec);  
```  
  
Then after it knows the size of the chunk it can read the rest of the chunk directly into the destination buffer.  
  
In the interface I propose the same can be achieved in the `read` method by adding a `remaining()` method to the parser.   
  
1) read the headers  
2) `read_some()` into the read_buffer  
3) `parser.write()` and copy the returned const_buffer() to the body buffer  
4) if the parser returns `error::need_more` then `read_some()` `parser.remaining()` bytes directly into the body buffer. Call `parser.write()` on them so the parser can update its internal counter  
5) goto 2.  
  
I mostly object about the burdening of the parser with the message concept and owning it. I believe the parser should own only its internal state and not the message. It should not know about it. It only cares about where to put the headers (Fields) and indicating the boundaries of the body. These should be passed in as parameters to `write()`.  
The message concept should be build in a layer above the parser so if one does not need it he is not required to have a message.

---

## Comment 56

> Username: vinniefalco  
> Created at: 2016-11-28 14:20:56 UTC  
> Updated at: 2016-11-28 14:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263282588  

>I mostly object about the burdening of the parser with the message concept and owning it. I believe the parser should own only its internal state and not the message.  
  
That's already exactly how it works, in both the current model and the new one!  
  
No `message` here:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/basic_parser_v1.hpp#L227  
  
Or here:  
https://github.com/vinniefalco/Beast/blob/http/test/http/new_parser.cpp#L306  
  
>Looking at the new_parser it is not completely zero copy there is a copy from the parse buffer to the body buffer around the chunk headers:  
  
Yes, there is a small amount of copying but that is constant with respect to the size of the body.

---

## Comment 57

> Username: georgi-d  
> Created at: 2016-11-28 16:31:39 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263319465  

I see, I think the `parser_v1` should be renamed to `message_parser_v1` so it is less confusing. I feel that too much of the parser logic currently lives in the `read()` method instead of the parser. For example I think `parser::consume()` method should not be explicit. The parser has enough state to know that if all the written bytes belong to the same chunk. It can also be combined with `write_body()` if `write()` is made to return a const_bufer for the location of the   body instead of copying.   
  
I am fine with `parser_v1`.  
  
The things I do not like too much about `basic_parser_v1` are:  
- the parser should not modify (consume) the buffers himself, it actually complicates the usage more than helps.  
- the parser should not both modify its internal state and copy buffers in the same method  
  
So the interface I propose is actually for `basic_parser_v1`. I think the `new_parser_v1 `can easily be modified to match it. This will allow for real zero copy operation.  
A `message_parser_v1` would then not even be needed because the message can be passed to the `read()` method by reference and just filled in in-place.

---

## Comment 58

> Username: vinniefalco  
> Created at: 2016-11-28 17:01:20 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263328196  

> I think the parser_v1 should be renamed to message_parser_v1  
  
I'm all in favor of bike shedding but let's save it for the formal review, where I am sure there are going to be many more opinions :)  
  
>I feel that too much of the parser logic currently lives in the `read()` method  
  
Do you mean in the current implementation (tip of the **master** branch) or do you mean in my new prototype? Are you talking about this `read` function?  
https://github.com/vinniefalco/Beast/blob/fd56b58464224dab4cffe5629843d15a6f68c33a/test/http/new_parser.cpp#L1200  
  
>A `message_parser_v1` would then not even be needed because the message can be passed to the read() method by reference and just filled in in-place.  
  
Are you suggesting that `read()` should call `msg.fields.insert()`? Or do you mean that `new_basic_parser_v1::write` should call `msg.fields.insert()` from `new_basic_parser_v1::parse_headers`? That would break callers that don't insert the fields. The reason that the parser is exposed as a base class, with callbacks to the derived class, is to relax the requirements on **Fields**. Consider the case where someone creates fields that use perfect hashing for known fields, and simply doesn't bother inserting for unknown fields.  The field name is represented using `int` instead of a string. The way to do this is to subclass `new_basic_parser_v1` and provide a custom implementation for `on_field`. But with your suggestion they have to rewrite `read`. And more importantly they have to rewrite `async_read`, which is a much more complex algorithm.  
  
I prefer not to require users to re-implement the complication asynchronous algorithms unless absolutely necessary (for example, in the relaying proxy case). And I think having relaxed requirements for **Fields** is preferable. There are already HTTP implementations in the wild that implement radically different strategies for representing fields, and I want to support them. Having the subclasses of `new_basic_parser_v1` allows that.  
  
Again I have to consider all the users of the library and that includes cases where the body is attached to the header in a `message` object, and it gets read in using a single API call.

---

## Comment 59

> Username: georgi-d  
> Created at: 2016-11-28 20:22:59 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263383302  

> Do you mean in the current implementation (tip of the master branch) or do you mean in my new prototype? Are you talking about this read function?  
> https://github.com/vinniefalco/Beast/blob/fd56b58464224dab4cffe5629843d15a6f68c33a/test/http/new_parser.cpp#L1200  
  
I am talking about the new prototype of `read`.   
  
When I said that the `parser_v1` should not be needed I meant that it would not be needed in its current form where it constructs and owns the whole message. There would definitely be need for a subclass which inserts into the `Fileds`. It would probably be called `fields_parser_v1` because the only functionality that is overridden is how the fields are handled. It would not need to construct and own the fields it can just be constructed with a reference to the fields by the `read`.  
  
I am not suggesting that the implementation of `read` or `async_read` should need to be changed to support different Fields or Body types. They could be re-implemented for a different body handling pattern though. For example a reverse proxy.

---

## Comment 60

> Username: georgi-d  
> Created at: 2016-11-29 22:30:46 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-263721432  

My main idea is that:   
- the `basic_parser_v1` should be derived from when a new fields handling logic is needed  
- the `read` and `async_read` methods should be re-implemented when a new body handling logic is needed.   
  
The current "new" implementation  requires both the `read` methods and the `new_basic_parser_v1` to be changed when a new handling of the body is needed.

---

## Comment 61

> Username: harrishancock  
> Created at: 2017-01-06 19:24:15 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-270983384  

@vinniefalco I haven't forgotten about giving you input on this, but need to let it rattle around my brain more.

---

## Comment 62

> Username: georgi-d  
> Created at: 2017-01-07 10:55:20 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271076584  

After thinking about this more what really do not feel right about the new implementation is the `parse_buffer` class which `read()` expects. It  does not allow any control over how the memory is allocated and deallocated.   
  
Also I do not like the fact that `new_basic_parser_v1::write` expects a ParseBuffer, it should expect a simple MutableBuffer and return how many bytes it has consumed from it indicating if it needs more data or not.  
  
I also believe that `read` and `async_read` should accept CompletionCondition because this way the provided implementations will cover much wider range of use cases and consumer of the library will almost never need to implement their own versions of them

---

## Comment 63

> Username: vinniefalco  
> Created at: 2017-01-08 21:42:15 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271181802  

>`read` and `async_read` should accept CompletionCondition  
  
I'm not so sure. Boost.Asio provides versions of `read` and `async_read` which take a **CompletionCondition**. But they are general purpose functions, not limited to any one protocol. I can only think of a very small number of possible completion conditions (maybe just one or two) that might be  useful for Beast's HTTP interfaces. To justify adding such a flexible parameter to the interface, we would have to anticipate that there are a broad spectrum of uses - and I'm just not seeing that with `beast::http::read` and related functions.  
  
How many different completion conditions can you think of, other than "the headers are complete?"

---

## Comment 64

> Username: harrishancock  
> Created at: 2017-01-09 02:13:05 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271200241  

### TL; DR  
  
@vinniefalco I hesitate to announce that I, too, am enamored with completion conditions, though in a slightly different way. :) I would note that **Parser** objects already seem to be very nearly a form of **CompletionCondition**.  
  
| How to ... | **Parser** | **CompletionCondition** |  
|---|---|---|  
| Signal when to stop | `p.complete()` returns `true` | `cond(ec, n)` returns `0` |  
| Feed the parser data | `p.write(b, ec)` | If `cond` holds a reference to the buffer, `dynabuf`, being streamed into, `cond(ec, n)` can call `dynabuf.data()` itself |  
| Feed the parser an EOF | `p.write_eof(ec)` | `cond(ec, n)` receives all IO error codes |  
  
The only sticking point is the handling of parser errors. The **Parser** concept returns them in out-parameters, which does not permit merging them with the result of `asio::(async_)read` without reimplementing that operation. I'd be very interested in exploring a design that did away with those out-parameters in favor of requiring manual checking of parser errors after reads (maybe with some notional `p.get_error()` function). The building blocks that Boost.HTTP would provide would be limited in focus to:  
  
- Parsers for specific production rules of interest in the HTTP grammar (header, body, body-chunk, trailer), to be used with `asio::(async_)read`  
- Synthesized attributes (e.g., `basic_fields`) which control how those parsers consume data  
  
### How I've done incremental parsing in the past  
  
*Disclaimer: I've never parsed HTTP.*  
  
I once had a need to perform incremental parsing of a very simple protocol. The primitive I settled upon, and which served me well, turned out to be [`asio::async_read_until`](http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/async_read_until/overload4.html) combined with a custom **MatchCondition**: for every partial read, my match condition received a pair of iterators representing the data received so far, which it would parse to see if it was done.  
  
I could just as well have used [`asio::async_read`](http://www.boost.org/doc/libs/1_62_0/doc/html/boost_asio/reference/async_read/overload4.html) with a **CompletionCondition**, provided the completion condition possessed a reference to the buffer or dynamic buffer being streamed into. Instead of receiving a pair of iterators, the completion condition receives the latest IO `error_code` and the number of bytes read, `n`, and returns the number of bytes it wants to read next. Armed with its hypothetical reference to the buffer being streamed into and the size `n` of the latest read, a completion condition can do anything a match condition can do. The `error_code` argument that it receives, however, allows the completion condition to see EOFs, which is something I don't think a match condition can do, and appears to be a crucial detail to properly support HTTP parsing.  
  
It is probably for these reasons that the Networking TS does not implement `(async_)read_until`, only `(async_)read` with **CompletionCondition**s: completion conditions seem strictly more general than match conditions.  
  
*Before I continue, let me acknowledge a caveat: `asio::(async_)read` currently only operates on `asio::streambuf` objects. The Networking TS, and presumably future versions of Asio, relaxes this requirement. Either way, the problem is trivially solvable.*  
  
### Nudging the Beast in this direction  
  
The heart of the engine that drives `http::(async_)parse` is a bunch of logic that loops around:  
  
```c++  
        // ... stuff ...  
        dynabuf.commit(stream.read_some(  
            dynabuf.prepare(read_size_helper(  
                dynabuf, 65536)), ec));  
        // ... more stuff ...  
```  
  
The `dynabuf.prepare(read_size_helper(...))` -> `stream.read_some(...)` -> `dynabuf.commit(...)` sequence **is** `asio::(async_)read`. The `read_size_helper(...)` call is the "I want more" return value of `asio::(async_)read`'s completion condition. The `// ... stuff ...` and `// ... more stuff ...` code is the body of the completion condition.  
  
Consider the current implementation of `http::parse`:  
  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/impl/parse.ipp#L252-L281  
  
And compare it to this (untested, because of the `asio::streambuf` caveat I mentioned above) reimplementation of the same function:  
  
```c++  
template<class DynamicBuffer, class Parser>  
auto  
make_dynabuf_parser(DynamicBuffer& dynabuf, Parser& parser, error_code& parser_ec)  
{  
    // Make a CompletionCondition that returns 0 when the parser is done  
    return [&, got_some = false]  
            (const error_code& ec, size_t /*bytes_read*/) mutable -> size_t {  
        if (ec == boost::asio::error::eof && got_some) {  
            parser.write_eof(parser_ec);  
            if (!parser_ec)  
                BOOST_ASSERT(parser.complete());  
        }  
        if (ec)  
            return 0;  
        auto used = parser.write(dynabuf.data(), parser_ec);  
        if (parser_ec)  
            return 0;  
        dynabuf.consume(used);  
        if (used > 0)  
            got_some = true;  
        if (parser.complete())  
            return 0;  
        return read_size_helper(dynabuf, 65536);  
    };  
}  
  
template<class SyncReadStream, class DynamicBuffer, class Parser>  
void  
parse(SyncReadStream& stream, DynamicBuffer& dynabuf,  
    Parser& parser, error_code& ec)  
{  
    // static_assert(arguments meet all requirements);  
  
    auto parser_ec = error_code{};  
  
    // Use asio::read to drive the algorithm  
    boost::asio::read(stream, dynabuf, make_dynabuf_parser(dynabuf, parser, parser_ec), ec);  
  
    if (!ec || ec == boost::asio::error::eof)  
        ec = parser_ec;  // caller will see eof on next read  
}  
```  
  
Kludgey and brittle, yes. But the point is: the current **Parser** concept can be shoehorned into a **CompletionCondition** with a simple wrapper that captures a reference to the **DynamicBuffer** being streamed into (a `make_buffer_parser` wrapper could be written that does the same thing for **BufferSequence**). The one wart is that the parser's error code has to be handled a bit circuitously. If users could accept the possibility of receiving `asio::error::eof` on a successful parse and the necessity of checking a notional `p.get_error()`, the **Parser** concept could be rewritten as either a refinement or factory of **CompletionCondition**s.  
  
I would justify the changes in error flow by noting the following:  
- Asio's philosophy says that EOF is an error. Experienced users could reasonably be expected to know this if Beast.HTTP provided parsers to be used with `asio::(async_)read`.  
- A parser error on untrusted HTTP source isn't so much a part of the local process' error flow rather than the remote process'. The server doesn't care that the client misspelled `GET`. The client cares. Sending the "you misspelled `GET`" error response to the client could be viewed as a non-exceptional, if unlikely, branch in the server code.  
  
That said, I do recognize that checking error codes might become a nightmare in practice. If users cannot accept the error restrictions, you could continue providing trivial, four-line implementations of `http::parse` and `http::async_parse` (`parse_op` would have two steps: 1. call `async_read`, 2. override the `error_code`). More advanced users would still be free to use the parser objects, and their associated completion conditions, as building blocks for more complex scenarios.  
  
### More complex scenarios  
  
While my `make_dynabuf_parser` is just an untested sketch, with some flexibility in the **Parser** concept, I can imagine a variety of factorizations. Consider if **Parser** were refined into three concepts:  
  
- **BufferParser**: The expression `p(cb)` returns a completion condition which parses data present in the `consuming_buffers<const_buffer> cb { b }`.  
  
   This would be useful for in-place parsers whose synthesized attributes contain references to data still in the buffer, e.g., a **Fields** type that contains a bunch of `string_ref`s pointing into the live data in `b`. Unparsed data overread would still be easily accessible through `cb.begin()`.  
  
- **BufferSequenceParser**: The expression `p(cbs)` returns a completion condition which parses data present in the `consuming_buffers<ConstBufferSequence> cbs { bs }`.  
  
   Like **BufferParser**, but useful for parsers whose synthesized attributes can handle non-contiguous regions of data, or which copy the data into their own memory.  
  
- **DynamicBufferParser**: The expression `p(db)` returns a completion condition which parses data present in the `DynamicBuffer db`.  
  
   Useful for parsers whose synthesized attributes always either copy or discard the actual data they consume, like the current `basic_fields` implementation, or a hashing fields implementation like @vinniefalco mentioned.  
  
Note that with these concepts, **Parser** objects still remain divorced from the memory buffers which may or may not back the data structures (synthesized attributes) into which they are reading. If you have a data overread in an HTTP header parser, you can instantiate an HTTP body parser and call `p(cb)({}, n)` to perform a partial parse of the overread (assuming we can figure out `n`) before passing a different completion condition of the same parser to an `asio::read` call to read the rest of the body.  
  
### Lastly ...  
  
This might be too invasive of the current design, so feel free to push back/veto. :) I don't know how well these ideas would work in practice, nor have I thought through how the generation/serialization side of things would/should work with completion conditions, nor do I think this is necessarily a solution to the "the user has to reimplement `async_read` to get anything complex done" problem.

---

## Comment 65

> Username: georgi-d  
> Created at: 2017-01-09 12:43:10 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271275390  

> I'm not so sure. Boost.Asio provides versions of read and async_read which take a CompletionCondition.  
  
There is a version: http://www.boost.org/doc/libs/1_61_0/doc/html/boost_asio/reference/async_read/overload2.html  
  
> How many different completion conditions can you think of, other than "the headers are complete?"  
  
I can immediately come up with a few conditions besides the "headers are complete one".  
  
1.  Read a large body in buffer of ~100KB then process that buffer (write to disk) and continue to the next buffer.  This will be very easy to implement with a CompletionCondition which internally sums up the size with which it is called until its internal total size becomes ~100k at which point it returns true (break).  
2. The body contains a series of independent messages (binary with a header which contains the length of the message) which need to be processes at a higher layer one by one. This is very easily implementable with a CompletionCondition which has internal reference to the read buffer. The condition will parse the static header and set an internal counter with the size of one message. On every invocation it will decrement its internal counter until it gets to 0 at which point it returns true (break)  
3. Process a large body in chunks as big as the ones that arrive over the wire. This can be implemented by passing a CompletionCondition which unconditionally returns true(break)  
  
 I have already worked on adding an overload of async_read with a CompletionCondition https://github.com/georgi-d/Beast/commit/c14400711f63b6b1010cd447ace0fce11fb0ada5 and it is trivial when added in the original implementation and a complete rewrite of the method if it is not available upfront.

---

## Comment 66

> Username: vinniefalco  
> Created at: 2017-01-09 14:53:17 UTC  
> Updated at: 2017-01-09 14:55:24 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271303756  

@harrishancock There are some great ideas there but I guess I should have provided more context and detail. We need to do away with the need for a dynamic buffer in the read interface. And I don't think that **Parser** should be a concept. Functions like `http::read` and `http::async_read` should not try to be general purpose, or allow for completion conditions. The redesign has these goals:  
  
1. **Parse entire header or chunk metadata in a single linear buffer**. I've looked at picohttpparser (https://github.com/h2o/picohttpparser/) and its different design choices give it the title of fastest performing HTTP parser. I would like Beast to at least be competitive. The new interface should provide a way for the entire HTTP header (or chunk metadata) to accumulate into a single linear buffer before it is parsed.  
  
2. **Eliminate buffer copies**. The current interface forces all header and body data to be copied twice. The header data is copied in order to convert multiple buffers into a single `string_view`. The body data is copied to transfer it into the message container. The header copying is eliminated by putting everything in one linear buffer before parsing. The new interface should allow the socket read to place (most of) the body data directly into the message container instead of first reading it into a dynamic buffer.  
  
3. **Support Expect: 100-continue**. Sending and handling the Expect header is problematic right now. A new interface should streamline this process.  
  
4. **Build an efficient relay/proxy**. Right now it is difficult or impossible to write a performant relay. It should be as simple as reading the headers, and then shuffling the body bytes from the client to the server. To do this in the current design requires an incredibly complicated "Writer" implementation, and ugly hacks with the "resume context." Its a mess. This is what I have so far on the relay function (not part of Beast just a proof of concept to show how the redesigned interface would work). Its got some problems with it that I want to iron out. Like the parse buffer thing.  
```  
/// Efficiently relay one HTTP message between two peers  
template<  
    bool isRequest,  
    class InAsyncStream,  
    class OutAsyncStream,  
    class MessageTransformation>  
void  
relay(  
    InAsyncStream& si,  
    OutAsyncStream& so,  
    error_code& ec,  
    boost::asio::yield_context yield,  
    MessageTransformation const& transform)  
{  
    using namespace beast::http;  
    parser_v1<isRequest> p;  
  
    // read the incoming message headers  
    for(;;)  
    {  
        // TODO skip async_read_some if parser already has the next set of headers  
        auto const bytes_transferred =  
            si.async_read_some(p.prepare(), yield[ec]);  
        if(ec)  
            return;  
        p.commit_header(bytes_transferred, ec);  
        if(ec == parse_error::need_more)  
        {  
            ec = {};  
            continue;  
        }  
        if(ec)  
            return;  
        break;  
    }  
  
    // Create a new message by transforming the input message  
    // At minimum this will remove Content-Length and apply Transfer-Encoding: chunked  
    auto req = transform(p.get(), ec);  
    if(ec)  
        return;  
  
    // send the header  
    async_write(so, req, yield[ec]);  
    if(ec)  
        return;  
  
    for(;;)  
    {  
        // TODO skip async_read_some if we already have body data  
        // Read the next part of the body  
        auto const bytes_transferred =  
            si.async_read_some(p.prepare(), yield[ec]);  
        if(ec)  
            return;  
        p.commit_body(bytes_transferred, ec);  
        if(ec == parse_error::end_of_message)  
            break;  
        if(ec)  
            return;  
  
        // Forward this part of the body  
        // p.body() removes any chunk encoding  
        //  
        if(buffer_size(p.body()) > 0)  
        {  
            async_write(so, chunk_encode(p.body()), yield[ec]);  
            if(ec)  
                return;  
        }  
    }  
  
    // Copy promised trailer fields from incoming request  
    // that are not already present in the outgoing response:  
    //  
    for(auto field : token_list{req.fields["Trailer"]})  
        if(! req.contains(field))  
            req.insert(field, p.get().fields[field]);  
  
    // Send the final chunk, including any promised trailer fields  
    //  
    streambuf sb;  
    write_final_chunk(sb, req);  
    async_write(so, sb.data(), yield[ec]);  
    if(ec)  
        return;  
}  
```  
  
I want to do away with the current "writer" concept, in particular the "resume context" feature. Its an unnecessary complication that doesn't work for the relay use-case. If people want to do things like save a file to the hard drive. they should be able to easily implement their own version of `http::read` or `http::async_read`.

---

## Comment 67

> Username: harrishancock  
> Created at: 2017-01-10 01:05:15 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271456972  

@vinniefalco Thanks for the problem statements, that helps clarify things. I think the design I sketched out can handle all of the use cases you described, as well as the use cases @georgi-d also recently described.  
  
I think the major detail with my proposal is that `basic_parser_v1` needs to be split up into several parsers, maybe in an `http::v1::parsers` namespace: `header`, `body`, `body_chunk`, `body_chunk_header`, etc. If that's not doable, then my suggestion is unfortunately dead-on-arrival.  
  
Another major detail is how to deal with overreads. In my initial post I dealt with that using `consuming_buffers`, but for now let me simplify it a bit and say, a **Parser** object returns the number of bytes read by its most recently constructed completion condition in `p.bytes_parsed()`.  
  
### @georgi-d's problem statements:  
  
> **1. Read a large body in buffer of ~100KB then process that buffer (write to disk) and continue to the next buffer.**  
  
As @georgi-d points out, this is easy to implement with a completion condition. However, `asio::(async_)read` makes it even easier, because it already guarantees not to overrun constant-sized buffers:  
  
```c++  
uint8_t body_buf[100_KB];  
auto b = boost::asio::buffer(body_buf);  
http::v1::parsers::body body_p;  
auto ec = error_code{};  
while (!body_p.complete() && !body_p.get_error() && !ec) {  
    boost::asio::read(s, b, body_p(b), ec);  
    // write `body_buf[0..body_p.bytes_parsed()]` to disk  
    // `body_buf[body_p.bytes_parsed(), n]` represents the overread, though in this particular case  
    // there is unlikely to be any  
}  
// handle errors if necessary  
```  
  
> **2. The body contains a series of independent messages (binary with a header which contains the length of the message) which need to be processes at a higher layer one by one.**  
  
The solution @georgi-d presents,  
  
> ... a CompletionCondition which has internal reference to the read buffer. The condition will parse ...  
  
is exactly the return value of the **Parser** `operator()` re-conceptualization I presented. Instead of using a `beast::http::body_parser` or whatever, you would implement your own special `my_body_sub_parser`, which you would use to implement your own `http::read` by composing it with a Beast `http::v1::parsers::header`. Using it in conjunction with chunked encoding would be tricky, but doable, given a Beast `http::v1::parsers::body_chunk_header` parser.  
  
> **3. Process a large body in chunks as big as the ones that arrive over the wire.**  
  
@georgi-d's solution (use a completion condition that returns unconditionally true) basically works, but I would note that it would actually have to return false on the first invocation, because completion conditions are called before IO. Here is an equivalent formulation using the design I proposed, based on the solution to problem 1:  
  
```c++  
uint8_t body_buf[4_KB];  
auto b = boost::asio::buffer(body_buf);  
http::v1::parsers::body body_p;  
auto ec = error_code{};  
while (!body_p.complete() && !body_p.get_error() && !ec) {  
    auto cond = [pcond = body_p(b), again = false](const error_code& ec, size_t n) mutable {  
        auto r = pcond(ec, n);  
        r = again ? 0 : r;  
        again = true;  
        return r;  
    };  
    auto n = boost::asio::read(s, b, cond, ec);  
    // process `body_buf[0..body_p.bytes_parsed()]`  
}  
// handle errors if necessary  
```  
  
One could also solve it by using `read_some` and feeding the bytes to the parser by hand, of course.  
  
### @vinniefalco's problem statements:  
  
> We need to do away with the need for a dynamic buffer in the read interface.  
  
The design I proposed relies on `asio::(async_)read`, which can handle single buffers, buffer sequences, or dynamic buffers -- user's choice.  
  
> And I don't think that Parser should be a concept.  
  
Curious, why not? Do you think parser objects should be an implementation detail, or perhaps revised and renamed?  
  
> Functions like http::read and http::async_read should not try to be general purpose, or allow for completion conditions. ... [Users] should be able to easily implement their own version of http::read or http::async_read.  
  
The design I proposed allows you to keep the interface of those functions as-is, but generalize to support buffers and buffer sequences in addition to dynamic buffers (or transition to supporting only buffer sequences, if that makes more sense), with a vastly simplified implementation. `(async_)read` is a wrapper around `(async_)parse`, which the design I proposed simplifies greatly: 4 lines versus 30 lines for the synchronous version, 2 states versus 4 for the async. The simplified implementations I proposed would give more advanced users an easy-to-follow blueprint to implement their own.  
  
> **1. Parse entire header or chunk metadata in a single linear buffer. ... The new interface should provide a way for the entire HTTP header (or chunk metadata) to accumulate into a single linear buffer before it is parsed.**  
  
If by "... before it is parsed" you mean "... before it is processed", then my proposed design solves this -- see the solution I gave to @georgi-d's first problem. If you really do mean "before it is parsed", then something like `asio::read_until(s, b, "\r\n\r\n")` (or just `\r\n` for chunk metadata) would do the trick.  
  
> **2. Eliminate buffer copies. ... The new interface should allow the socket read to place (most of) the body data directly into the message container instead of first reading it into a dynamic buffer.**  
  
My proposed design differs slightly here, as it assumes that the message data would live in `mutable_buffer`s (maybe they are owned by a `streambuf`, maybe not), and that the data would be parsed in-place (i.e., not copied, just pointed to by `string_view`s). This does, however, leave the problem of writing it back out to a different socket (or to disk), and I haven't really thought about that part yet.  
  
> **3. Support Expect: 100-continue.**  
  
My proposed design would support this by separating the header and body parsers into two different parsers. One would first read/parse the header, then reply to the remote with 100 if necessary, parse the overread from the header's IO operation, then read/parse the rest of the body.  
  
> **4. Build an efficient relay/proxy.**  
  
The big kahuna. :) Your proof-of-concept breaks the header and body parsing into two separate functions (`commit_header`, `commit_body`). My idea breaks the header and body parsing into two separate objects. Perhaps we might find some convergence of ideas inside the reimplementation of `parser_v1`.  
  
I have to go, but I will try to come up with a proof-of-concept tonight. Up to now I've just been all talk and no code!

---

## Comment 68

> Username: vinniefalco  
> Created at: 2017-01-10 01:57:04 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271464746  

@harrishancock You can't split the body and header into separate objects, because of trailers.   
  
>If you really do mean "before it is parsed"  
  
Yes I mean before its parsed. `boost::asio::read_until` requires a **DynamicBuffer**.  
  
You've got good intentions but there are problems with the approach. In particular its not clear how you would deal with `read` operations which return a buffer that has some body data mixed in with chunk delimiters and chunk extensions.  
  
Really the only way to vet this idea is to actually write the two functions `async_read` and `async_relay`. Signature for `async_relay` would look something like this:  
```  
/// Efficiently relay one HTTP message between two peers  
template<  
    bool isRequest,  
    class InAsyncStream,  
    class OutAsyncStream,  
    class MessageTransformation>  
void  
relay(  
    InAsyncStream& si,  
    OutAsyncStream& so,  
    error_code& ec,  
    boost::asio::yield_context yield,  
    MessageTransformation const& transform);  
```  
  
This version is hard-coded to work with coroutines (see new_parser.cpp) but you could replace that with a completion token.

---

## Comment 69

> Username: vinniefalco  
> Created at: 2017-01-10 01:58:04 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271464908  

@harrishancock This is the most up-to date proof of concept for `read` that I have managed to come up with. It deals with everything, including chunk extensions and trailers:  
```  
template<class SyncReadStream,  
    bool isRequest, class Body, class Fields>  
void  
read(SyncReadStream& stream, parse_buffer& buffer,  
    message<isRequest, Body, Fields>& msg, error_code& ec)  
{  
    using boost::asio::buffer_copy;  
  
    new_parser_v1<isRequest, Body, Fields> p;  
  
    // Read and parse header  
    for(;;)  
    {  
        p.write(buffer, ec);  
        if(! ec)  
            break;  
        if(ec != error::need_more)  
            return;  
        ec = {};  
        auto const bytes_transferred =  
            stream.read_some(buffer.prepare(  
                buffer.size() + 1), ec);  
        if(ec)  
            return;  
        buffer.commit(bytes_transferred);  
    }  
  
    typename Body::reader r{p.get(), p.content_length()};  
  
    // Read and parse body  
    while(! p.complete())  
    {  
        // maybe read chunk delimiter  
        for(;;)  
        {  
            p.write(buffer, ec);  
            if(! ec)  
                break;  
            if(ec != error::need_more)  
                return;  
            ec = {};  
            auto const bytes_transferred =  
                stream.read_some(buffer.prepare(  
                    buffer.size() + 1), ec);  
            if(ec)  
                return;  
            buffer.commit(bytes_transferred);  
        }  
  
        // copy body bytes in buffer  
        p.write_body(r, buffer, ec);  
        if(ec)  
            return;  
  
        // read remaining part of chunk  
        auto const remain = p.remain();  
        if(remain > 0)  
        {  
            auto const b = r.prepare(remain, ec);  
            if(ec)  
                return;  
            auto const bytes_transferred =  
                stream.read_some(b, ec);  
            if(ec == boost::asio::error::eof)  
            {  
                ec = {};  
                p.write_eof(ec);  
                if(ec)  
                    return;  
                BOOST_ASSERT(p.complete());  
            }  
            else  
            {  
                if(ec)  
                    return;  
                r.commit(bytes_transferred, ec);  
                if(ec)  
                    return;  
                p.consume(bytes_transferred);  
            }  
        }  
    }  
    r.finish(ec);  
    if(ec)  
        return;  
    msg = p.release();  
}  
```

---

## Comment 70

> Username: harrishancock  
> Created at: 2017-01-10 21:54:25 UTC  
> Updated at: 2017-01-11 00:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271710011  

>You can't split the body and header into separate objects, because of trailers.  
  
Good point. My design would require separate parser objects to contain references to the same fields object, in that case.  
  
> Yes I mean before its parsed. boost::asio::read_until requires a DynamicBuffer.  
  
You can use `asio::read` to accomplish the same thing. Here is a working demo, tested on Linux. It makes a single call to `asio::read` to read data into a contiguous buffer until it sees CRLF CRLF, checks to see if it got the full body as well, and initiates a second read if not. To find the end of the body, it just searches for the magic substring "01234567890123456789".  
  
https://github.com/harrishancock/Beast/blob/read-until-delim/examples/read_until_delim.cpp  
  
And test output:  
  
https://gist.github.com/harrishancock/f5ff6bc92ea149071f20893830d9c06d  
  
Note that it:  
• has no loops  
• has no deep buffer copies  
• does not use dynamic buffers  
• has only two calls to `read`: once for the header, once for the body suffix, if necessary  
• handles delimiters that straddle boundaries between calls to `asio::read`  
• abstracts the pattern matching code into a completion condition  
• has a lot of buffer arithmetic  
  
I agree the only way to really vet the idea is to implement `async_read` and `async_relay`. I'm motivated to do this, but I also recognize such a refactor would be time-consuming and perhaps not worthwhile, given that your current `read` draft is already quite clean!  
  
Do you have the code up on a branch? I'd be curious to see the implementation of `parse_buffer`.

---

## Comment 71

> Username: vinniefalco  
> Created at: 2017-01-11 00:37:16 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271743045  

GRRR... it looks like I edited your post instead of replying!! UGH

---

## Comment 72

> Username: vinniefalco  
> Created at: 2017-01-11 00:37:57 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271743152  

>My design would require separate parser objects to contain references to the same fields object, in that case.  
  
So in the body parser how will you know the content length, know if the transfer-encoding is chunked, or know if an EOF is required to detect the end of message? Are you going parse the header fields twice? After headers are parsed, there is contextual information that the body parser needs in order to do its job. That's why I have avoided the split parser design. In fact to construct the body parser in the current model you pass in the header parser:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/http/parser_v1.hpp#L139  
  
Its clumsy.  
  
>You can use asio::read to accomplish the same thing. Here is a working demo, tested on Linux.  
  
Your example does not allow the buffer to grow. What if I just want to allocate a 1024 byte buffer to handle the most common cases, and then if it doesn't find the CRLFCRLF after filling that buffer it will reallocate a bigger buffer? In your implementation you would have to always allocate a buffer sized to hold the largest allowed header. This is wasteful when building servers that handle 10k+ concurrent connections.  
  
>your current read draft is already quite clean!  
  
Its got some issues. The requirement to have a parse_buffer is odious. I'm working on implementing the relay version and also refactoring the derived parser to take a reference to the message.  
  
>Do you have the code up on a branch? I'd be curious to see the implementation of parse_buffer.  
  
https://github.com/vinniefalco/Beast/commits/http  
  
Its not quite where I'd like it to be. But getting there!

---

## Comment 73

> Username: harrishancock  
> Created at: 2017-01-11 01:42:13 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271753840  

> [threading context state through parsers]  
  
My approach would basically boil down to the same thing: pass the necessary information into the body parser's constructor. I think you've convinced me there's little benefit trying to split up the parsers.  
  
> Your example does not allow the buffer to grow. What if I just want to allocate a 1024 byte buffer to handle the most common cases, and then if it doesn't find the CRLFCRLF after filling that buffer it will reallocate a bigger buffer?  
  
More challenging to generalize to that, but I think it's still feasible. I'll update my example tomorrow to explore how I would do it and still handle the ConstBufferSequence case.  
  
Thank you for sharing the code! I take it `parse_buffer` is a DynamicBuffer guaranteed to always return linear buffers from its methods?

---

## Comment 74

> Username: vinniefalco  
> Created at: 2017-01-11 01:43:09 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271754006  

>Thank you for sharing the code! I take it parse_buffer is a DynamicBuffer guaranteed to always return linear buffers from its methods?  
  
Yeah its something like that. It feels icky though.

---

## Comment 75

> Username: harrishancock  
> Created at: 2017-01-11 02:19:20 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271759320  

I guess I have two pieces of feedback on it:  
  
1. `parse_buffer<Alloc>` would be a nice addition.  
  
2. I would have expected the new parser to be generalized over DynamicBuffer rather than the more specific ParseBuffer. Is that what feels icky to you? `buffers_begin()` in place of `buffer_cast<>()` and a deduced `Iter` parameter type in place of `const char*` should make it work for both, and the magic of C++ should in theory optimize both cases ...  
  
   Of course I know the magic sometimes fails, and I imagine you've already thought about this stuff?

---

## Comment 76

> Username: vinniefalco  
> Created at: 2017-01-11 03:01:38 UTC  
> Updated at: 2017-01-11 03:02:58 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271765125  

What you described with `buffers_begin` is the fast train to bad performance. One of my goals it to make the parser faster. The picohttpparser I linked earlier outperforms the nodejs parser (upon which the Beast is based) by quite a large margin. With the linear buffer you can use SSE / special instructions. See:  
https://github.com/h2o/picohttpparser/blob/master/picohttpparser.c#L104  
  
pico parser is 3x faster than nodejs parser, check out the benchmark at the bottom of this page:  
https://github.com/h2o/picohttpparser  
  
Check out this presentation, its from the author of H2O (also the author of picohttpparser)  
http://www.slideshare.net/kazuho/h2o-20141103pptx

---

## Comment 77

> Username: georgi-d  
> Created at: 2017-01-11 08:44:08 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271810709  

@vinniefalco I think the `parse_buffer` should be replaced by a `ContinuousDynamicBuffer` concept which is just a `DynamicBuffer` that guarantees that `prepare()` returns a single continuous `mutable_buffer`.  
  
I also feel that `Body::reader` is just another  incarnation of `DynamicBuffer` with the only difference being that instead of `commit(size_t)` it has `commit_partial(start, size_t)` which can be called multiple times so parts of the buffer can be skipped.

---

## Comment 78

> Username: vinniefalco  
> Created at: 2017-01-11 14:27:40 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-271881899  

>I also feel that `Body::reader` is just another incarnation of `DynamicBuffer`  
  
It is definitely similar, and I borrowed heavily from the **DynamicBuffer** concept, but note that `reader` provides a means to construct with the content length if it is known.

---

## Comment 79

> Username: vinniefalco  
> Created at: 2017-02-07 07:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-277918383  

I have made some forward progress. One of the goals of the new interface is for the new parser to receive the entire header in a single buffer, allowing some great optimizations (ideas taken from picohttpparser). Previously I handled this with a new class `parse_buffer` which people (myself included) thought was ugly.  
  
Now, I have changed the parser so it can take any object meeting the requirements of **DynamicBuffer**. If the passed input buffer sequence has length one, then it performs the parse using that buffer. If the input buffer sequence has length greater than one, the parser allocates temporary memory and copies the buffers so that it can perform the parse on a single linear buffer.  
  
To mitigate the expense of the additional allocate and copy cycle, I have added a new class `flat_streambuf` which meets the requirements of **DynamicBuffer** and offers an additional guarantee: all returned buffer sequences will be of length one. Using `flat_streambuf` with the new http read functions will assure that the fast code path is always taken (avoiding the allocation).  
  
`flat_streambuf` is here:  
https://github.com/vinniefalco/Beast/blob/http-8/include/beast/core/flat_streambuf.hpp  
  
And here is the new parser, read operation, and test code (this is still very much a work in progress)  
https://github.com/vinniefalco/Beast/blob/http-8/test/http/new_parser.cpp  
https://github.com/vinniefalco/Beast/blob/http-8/include/beast/http/new_parser.hpp  
  
What does this mean? No more `parse_buffer`, and you can use your choice of  dynamic buffer when calling read.  
  
I am also making another change, when `http::read` calls `dynabuf.prepare` it will catch `std::length_error` and translate it to a custom error code. This will make it easier for callers to take over management of the memory used to represent the dynamic buffer, since presently `async_read` will throw an exception if `prepare` attempts to exceed the maximum size of the dynamic buffer.

---

## Comment 80

> Username: vinniefalco  
> Created at: 2017-02-18 22:56:18 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-280881511  

Just to let you know, I have made considerable progress on this issue. In a few weeks I will be done and it will perform all the operations that we have contemplated here, including the ability to manage the  
buffer yourself. Preliminary benchmarks show that the new parser is 100% faster than the old one, and I still have many possible optimizations remaining to implement. You can try the branch here:  
https://github.com/vinniefalco/Beast/tree/http  
  
There is still a little bit of work to do on the split parsing, but the new design will make that totally doable.

---

## Comment 81

> Username: georgi-d  
> Created at: 2017-02-26 16:11:40 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-282566544  

After reasoning about the new implementation the `basic_parser`  looks like a good Push(SAX like) interface. I have just two comments for it:  
  
1) I do not like the `maybe_flatten` functionality which breaks the promise that the parser will not allocate memory. It is also very likely that if the caller could not provide a continuous buffer the parser will also not be able to allocate one. I think `basic_parse` should either  not expose an interface with non continuous buffer or have a separate parsing logic for it.  
  
2) The `basic_parse::write_body` method is unused and I think should not be there. The basic parser should not be burdened with the Reader concept.  
  
For the `header_parser` and the `message_parser` I do not like the fact that they instantiate their own copies of the Fields and the Message respectively. This prevents the Fields and Message from forwarding the input to some application state which limits the Fields and Message to just some sort of containers which copy state into themselves. I would suggest for the `parser` to hold just a reference to the Fields or Message. The issue is very obvious in the `read` method which receives reference to a Message but then creates a parser which has its own instance and then blindly overwrites the passed in Message.  
  
I would prefer if the `base_parser` was not Push model  where one `write` can result into multiple events/callbacks without much control of stopping the parser but a Pull model where each `write` returns one event. This way the caller has full control how many events it wants to process and does not need a complex state machine to track the push events.  
  
Actually implementing a Push wrapper on top of a Pull one is trivial while the other way around very hard and unfriendly to the optimizer.  
  
Lastly I would really like for `read/parse` and `async_read/parse` to receive a CompletionCondition which would greatly improve their usefulness.

---

## Comment 82

> Username: vinniefalco  
> Created at: 2017-02-26 18:10:20 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-282574706  

Thanks for looking at this!  
  
>I do not like the maybe_flatten functionality which breaks the promise that the parser will not allocate memory. It is also very likely that if the caller could not provide a continuous buffer the parser will also not be able to allocate one. I think basic_parse should either not expose an interface with non continuous buffer or have a separate parsing logic for it.  
  
`basic_parser` only promises not to allocate memory if the passed buffer sequence contains a single element. Having the parser accept a **ConstBufferSequence** naturally fits into the existing Asio framework. The `streambuf` class implementation provided by Beast does not guarantee a single contiguous buffer. It does this for performance (no need to allocate/copy/free). If you want to avoid the parser's memory allocation, the `flat_streambuf` class is provided which guarantees a single contiguous buffer. Or, you can use your own implementation and just go through the `boost::asio::const_buffers_1` overload of `write`.  
  
Limiting the parser to single buffers would affect the signatures of `http::read` and `http::parse`. It would mean you could no longer use any object meeting the requirements of **DynamicBuffer**. So that's not a viable option.  
  
Having an entire separate parser just to handle the case where the buffers are discontiguous sounds quite cumbersome. This means two different parsers to debug. And worse, performance tests show that parsing one character at a time (like the old Beast/nodejs parser) is slower than going through the `maybe_flatten`!  
  
Since the outcome of whether or not `basic_parser` allocates memory is entirely under control of the caller, I think the current implementation is reasonable.  
  
>The basic_parse::write_body method is unused and I think should not be there. The basic parser should not be burdened with the Reader concept.  
  
Yeah, I agree. I"m still trying to work that out.  
  
>For the header_parser and the message_parser I do not like the fact that they instantiate their own copies of the Fields and the Message respectively.  
  
Yeah, I know what you mean. The problem is that if the interface of the derived parsers use a reference then some invariants can be broken. For example you could construct a message parser from a header parser but construct it with a different set of fields from what the header parser already parsed. Then HTTP trailers would go into the wrong container.  
  
Another reason for making the parser own the message is that it allows for implementations where the parser owns the storage for field container objects. Otherwise, it forces a buffer copy.  
  
>This prevents the Fields and Message from forwarding the input to some application state which limits the Fields and Message to just some sort of containers which copy state into themselves.  
  
Can you give me a brief example?  
  
>The issue is very obvious in the read method which receives reference to a Message but then creates a parser which has its own instance and then blindly overwrites the passed in Message.  
  
True, but `read` is for very simple uses of HTTP. Any serious client or server is going to have to use `parse` so they can handle `Expect: 100-continue`. And so they defer the commitment of a **Body** type until after the headers are available. So I think the behavior you described is okay.

---

## Comment 83

> Username: cbodley  
> Created at: 2017-03-07 22:04:14 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-284874815  

Hi @vinniefalco,  
  
I'm very interested in this feature, and have been following your progress closely. I'm working on a prototype that uses Beast as a replacement for the existing frontend of [Ceph's S3 gateway](http://docs.ceph.com/docs/master/radosgw/). The ability to stream data for large object uploads is extremely important for this use case.  
  
I've updated this prototype to use your https://github.com/vinniefalco/Beast/commits/http branch, and have been very happy with the interface changes overall. The split between `header_parser` and `message_parser` is a good fit for applications whose request processing logic is already split between header and body.  
  
My only struggles have been with the parser implementation itself. My code is very similar to the last "proof of concept" you posted above, where the `// maybe read chunk delimiter` section calls `parser.write()` repeatedly until it succeeds. I've identified some cases where `basic_parser::write()` is able to make quite a bit of progress, but then returns 0 when `parse_chunk()` needs a bit more data. Because the parser updated its internal state as it made progress, the next call to `write()` with a complete buffer fails to parse correctly. I've pushed a branch to https://github.com/cbodley/Beast/commits/http-for-rgw that adds a unit test for this, and would be willing to write some more extensive tests as well, if that helps.  
  
However, as @georgi-d suggested above, using a pull-based model that parses a single unit at a time seems like a good way to keep the parser state consistent, and might eliminate that class of potential bugs entirely.

---

## Comment 84

> Username: vinniefalco  
> Created at: 2017-03-07 22:50:33 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-284886537  

@cbodley unit tests illustrating problems are very much welcomed! But it looks like you also fixed it?  
  
I have not yet invested heavily in making the parser bug-proof, because of the larger design problems with handling the body for the direct-write and relay cases. This version of the parser is easier to fix though, since it does away with the clunky "state" and one-char-at-a-time technique used by the node-js that the old Beast parser emulates.  
  
Fair warning, the `basic_parser` member functions below this comment:  
```  
// VFALCO Stuff below here is in flux  
```  
  
are clumsy and quite ugly, as @georgi-d has pointed out. They will certainly be changed.  
  
I'm having a bad case of writers block at the moment, figuring out how to clean up this last little design problem. All of the solutions so far lack that simple elegance that the rest of Beast exhibits. I'm waiting for a bit of inspiration that will make things "click" to where we get an interface that is intuitive.  
  
@georgi-d has proposed ideas about completion condition and a pull-based model. I think his ideas come from the general frustration with the current design (which again I admit  is not ready for production). But I still think there exists a better way.  
  
Thank you for your comments they are greatly appreciated!

---

## Comment 85

> Username: vinniefalco  
> Created at: 2017-03-07 22:57:15 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-284888224  

@cbodley What the hell was I thinking, **Reader** returns `boost::optional<...>` from `prepare`? Wow, that's ugly!

---

## Comment 86

> Username: georgi-d  
> Created at: 2017-03-11 16:35:50 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-285879309  

>   
>   
>     > This prevents the Fields and Message from forwarding the input to some application state which limits the Fields and Message to just some sort of containers which copy state into themselves.  
>   
>   
> Can you give me a brief example?  
  
In general this means that `read` and `async_read` are not usable with any non default constructable messge type. One use case I can think of is if the `Fields` for example is implemented to call an application callback for each header. The callback needs to be initialized in the message but as `read` is currently implemented there is no way.  
  
On the push vs pull model: The current base_parser can fairly simply be turned into a pull based interface by `write()` returning back to the caller at every point it currently would call one of the `on_*` methods indicating which one it would have called and with what parameters.

---

## Comment 87

> Username: vinniefalco  
> Created at: 2017-03-11 19:07:04 UTC  
> Updated at: 2017-03-11 19:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-285892346  

>this means that read and async_read are not usable with any non default constructable messge   
  
Yes, that is by design. `read` and `async_read` are functions for simple, light-duty operations. These simple functions cannot do split parsing. For anything advanced (such as a robust server) you're going to have to use `parse` and `async_parse`.   
  
Another example of a **Fields** that is non-constructible, is one that uses a custom allocator which is also not default-constructible.

---

## Comment 88

> Username: mfojtak  
> Created at: 2017-03-13 10:00:24 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-286062916  

Sorry but how do I write and read chunked message one chunk at the time?   
The API seems to be getting more and more confusing. Is there a simple example on how to read and write chunked message? Thanks.

---

## Comment 89

> Username: vinniefalco  
> Created at: 2017-03-13 12:07:43 UTC  
> Updated at: 2017-03-13 12:08:03 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-286089444  

>The API seems to be getting more and more confusing. Is there a simple example on how to read and write chunked message?  
  
Keep in mind that all of the discussion and changes taking place in this thread are in a separate branch, the API at the tip of the _master_ hasn't changed. Unfortunately its not possible to read chunked messages in the manner you describe - this is being worked on.  
  
To write a chunked message, first write the header using the appropriate overload of `write` or `async_write` (such as http://vinniefalco.github.io/beast/beast/ref/http__async_write/overload1.html). Then use the `chunk_encode` function to encode your chunks (see http://vinniefalco.github.io/beast/beast/ref/http__chunk_encode.html).

---

## Comment 90

> Username: mfojtak  
> Created at: 2017-03-13 18:05:06 UTC  
> Updated at: 2017-03-13 18:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-286192783  

> Then use the chunk_encode function to encode your chunks (see http://vinniefalco.github.io/beast/beast/ref/http__chunk_encode.html).  
  
What do I do with those chunks next? Should I call async_write again for each chunk? How do I create message from ConstBufferSequence?  
  
I also found file_body example with custom writer implementation. How does it work? Would each call to writer::write be wrapped into a separate chunk? In this case, the file would be chunked...  
  
I am trying to implement http video streaming solution. I am using asio stateful coroutines and subsequent calls to async_write fit much better into my concept as oppose to writer pattern.  
  
If I understand it correctly, the chunked message read will be implemented also as a subsequent calls to async_read.  
  
I know that it is all happening in http branch but looking into the new code being added I cannot get my head around of what the future state will be like.

---

## Comment 91

> Username: vinniefalco  
> Created at: 2017-03-13 19:27:29 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-286216678  

>What do I do with those chunks next? Should I call async_write again for each chunk? How do I create message from ConstBufferSequence?  
  
After you write the `beast::http::header` using the appropriate `beast::http::async_write` overload, you use `boost::asio::async_write` (note: we're using the **boost** function here) on the return value of `beast::http::chunk_encode`.  
  
>I also found file_body example with custom writer implementation. How does it work?  
  
I would not use the `file_body` from the examples. Instead, just implement your own class that reads the file and presents chunks of some size, and pass those pieces into `beast::http::chunk_encode`.  
  
> I am using asio stateful coroutines and subsequent calls to async_write fit much better into my concept as oppose to writer pattern.  
  
I agree, for streaming video the writer pattern isn't very useful. The **Writer** pattern is only useful for a narrow set of cases such as when the body is std::string` or a `streambuf`. I will make that more clear in the documentation once the new interfaces are stabilized.  
  
>looking into the new code being added I cannot get my head around of what the future state will be like.  
  
I agree! And I am not sure myself...I am still working on it. Most likely it will be different from what is there.  
  
Thanks!

---

## Comment 92

> Username: vinniefalco  
> Created at: 2017-03-23 20:57:54 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-288857693  

I have made significant progress on this branch, it is close to being complete!!!  
https://github.com/vinniefalco/Beast/commits/http

---

## Comment 93

> Username: mika-fischer  
> Created at: 2017-03-24 09:19:26 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-288971841  

Great news! Are there examples for the usage of the new API or generated docs for the http branch?

---

## Comment 94

> Username: vinniefalco  
> Created at: 2017-03-24 10:59:06 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-288992771  

@mika-fischer I'm still working on polishing this up and filling out the test coverage. The main change is to the interface of `basic_parser` and to the HTTP algorithms. The parser has a `write_some` member which advances the state in a predictable way. And you can query the parser state, this lets you see each chunk in a chunk-encoded body and interact with it. There are also new free functions `parse_some` and `async_parse_some` which only perform up to a single socket read. Finally, the parser supports an additional interface for reading data directly into the body container, eliminating the unnecessary memory copy. You can still do the memory copy if you want but there's a way to avoid. The new parser is significantly faster than the old Beast parser which was based on the nodejs parser.

---

## Comment 95

> Username: vinniefalco  
> Created at: 2017-03-27 01:50:29 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-289335515  

@georgi-d It only took six months, but I have finally developed a solution that addresses your use case! And its an interface that I can be happy with. Its called `parse_some` (along with some other tweaks). Here is a test that shows you exactly how to read the header, and then read the body in chunks as you desire:  
https://github.com/vinniefalco/Beast/blob/90d4e2438f3d79fe0d2210d00a2ad5cc2bfa3172/test/http/design.cpp#L225

---

## Comment 96

> Username: mika-fischer  
> Created at: 2017-03-29 14:39:39 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290111117  

Can you give me a pointer as to how to achieve the following with the new API:  
  
1. After reading the request header, read the request body content using a fixed-size buffer, i.e. read the body in chunks of 4k for instance. The end of the body should be indicated somehow and chunk-encoding should be stripped by beast.  
  
2. After preparing and sending out the response header, write the response body in chunks of 4k, so that beast either adds chunk encoding (if no Content-Length in header) or not. In the latter case, it should raise an error if more data is written than allowed.  
  
In the case that the current API cannot do this, would you consider adding it? Otherwise, can you give pointers as to how to do this myself in a safe and efficient way?

---

## Comment 97

> Username: vinniefalco  
> Created at: 2017-03-29 15:36:01 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290129353  

1. https://github.com/vinniefalco/Beast/blob/0b69724c3fa1c5181d2e73df857edea3e0387301/test/http/design.cpp#L539

---

## Comment 98

> Username: vinniefalco  
> Created at: 2017-03-29 22:02:53 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290240330  

@mika-fischer I don't understand 2 above, how could more data be written than allowed?

---

## Comment 99

> Username: vinniefalco  
> Created at: 2017-03-30 04:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290297407  

@mika-fischer Okay the first iteration was pretty terrible, but I have managed to make it incredibly lean - I think you will like this! @georgi-d I think you will like it too!!! Here it is:  
  
https://github.com/vinniefalco/Beast/blob/1c0b8f5b68f750c32816beb6c7dcd87398a82f77/test/http/design.cpp#L432

---

## Comment 100

> Username: mika-fischer  
> Created at: 2017-03-30 08:12:21 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290335614  

Yes, this one is much better. But still, I think it's much too complicated. It's great that it's possible to use beast in that way, but I think as a general purpose HTTP API, this needs to be possible without caring about parser states and without so many possibilities for errors.  
  
Im my mind, there are two use-cases which probably cover more than 90% of uses for a HTTP library:  
1. Read / write the whole message in one go. This is already well covered with beast.  
2. Read/write header, then read/write body in a streaming fashion _without having to care about parser internals or HTTP internals_. I just want to read/write my file upload in fixed-size blocks. The API I would like to have for this (reading) is a pull API where I give a buffer to beast and it puts the next `buffer_size` bytes of the body in there and tells me how much it read and if the body is finished. Similarly for writing. Beast should figure out from the headers if chunk encoding is necessary and do it transparently.  
  
Then there are advanced use-cases where access to parser state, chunks from the HTTP chunk encoding etc. are important. This is also covered well already by beast.  
  
I just think this library could be much more useful if point 2 above were already included, and easy to use in a safe way _without knowing much about beast internals or HTTP_.

---

## Comment 101

> Username: vinniefalco  
> Created at: 2017-03-30 11:55:01 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290388625  

Can you provide an example function illustrating how such an interface might look?

---

## Comment 102

> Username: mika-fischer  
> Created at: 2017-03-30 13:30:27 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290410935  

Well, there are many possibilities. Maybe it could be added to `message_parser` (but with which body type?), or work via a new `stream_body` type for `message_parser`, or a separate `streaming_body_parser` object with `async_read` members, or just with free functions. I don't really care as long as it's simple to use.  
  
```  
beast::streambuf beast_buffer;  
http::header_parser<true, http::fields> header_parser;  
async_parse(socket, beast_buffer, header_parser, yield);  
  
http::streaming_body_parser body(socket, beast_buffer, header_parser);  
  
char buffer[4096];  
beast::error_code ec;  
while (true) {  
    size_t bytes_read = body.async_read(yield[ec]); // This will strip out chunk-encoding  
    if (ec) // ec will be eof when body is finished  
        break; // error handling omitted here...  
    // do something with block  
}  
```  
  
I'm sure I'm glossing over important details again, but the general idea should be clear.

---

## Comment 103

> Username: vinniefalco  
> Created at: 2017-03-30 14:19:17 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290425109  

Remember that Beast is a low level library, its not for end users. It is targeted towards other library writers, so they can build higher level interfaces. I think that the example that you gave, it skips a lot of important things like the chunk extension. I think you could build the streaming_body_parser described in your example code. But I don't think it should be part of the library.  
  
I think that the function I provided, with the states, is okay. Its pretty lean.

---

## Comment 104

> Username: mfojtak  
> Created at: 2017-03-30 14:34:32 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290430098  

What I don't understand though is that if I want to read a chunked http message in one piece, I can do it in nice "high-level" manner and all internals of beast are hidden to me.  
Why the user has to enter lower level of beast only to be able to read one chunk at the time?  
  
Btw., why should I care if the message is chunked in the first place. I read a message in a loop until it is finished.  
  
parser p;  
header h = p.read_header();  
while(!p.end())  
{  
  message m = p.read_some();  
}  
  
I agree that the new API is much better. But it still forces user to have a preliminary knowledge about the message he parses. ASIO hides internals of TCP, beast hides internals of websockets, why not HTTP?

---

## Comment 105

> Username: mika-fischer  
> Created at: 2017-03-30 14:39:48 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290431755  

Well, it can still be used for low-level things, I'm not suggesting to remove the low-level interface.  
  
Chunk extensions are a non-issue, just parse and ignore them for this use-case. If a user really cares about them, it's OK to use the low-level interface. Are there _relevant_ details that stand in the way of such an API?  
  
I have to say that I find it quite strange that you include a simple interface (`http::request` / `http::response`), which is so simple that it can really only be used for toy examples, but don't want to include an interface that would be much more useful.  
  
I'm also surprised that you don't want this library to be used by normal application writers. I really think it would increase the chances of this library being accepted into boost if it was more widely applicable than to writers of http libraries...

---

## Comment 106

> Username: cbodley  
> Created at: 2017-03-30 16:39:10 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290468696  

I was able to implement a simple `streaming_body` class that works with the existing interfaces to accomplish this in a way that I thought was pretty elegant. Is something like this sufficient?  
  
https://gist.github.com/cbodley/a25282de38286f4cba8f172f9f5f9dc6

---

## Comment 107

> Username: vinniefalco  
> Created at: 2017-03-30 16:52:55 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290472638  

> I read a message in a loop until it is finished.  
  
You can do that now:  
```  
    do  
    {  
        auto const bytes_used =  
            parse_some(stream, dynabuf, parser, ec);  
        if(ec)  
            return;  
        dynabuf.consume(bytes_used);  
    }  
    while(! parser.is_complete());  
```  
  
>...you include a simple interface...which is so simple that it can really only be used for toy  
> examples, but don't want to include an interface that would be much more useful.  
  
I think you mean `read` and `async_read`, but these handle the very common case where you want the body represented by a container in memory and don't need to see the bytes as they pass by. Your use case is covered by the current API. However, I will still put more energy into trying to streamline it further.  
  
>you don't want this library to be used by normal application writers. I really think it would increase the chances of this library being accepted into boost if it was more widely applicable than to writers of http libraries...  
  
I believe the reason that we do not have a robust HTTP solution in C++ yet, is because previous efforts to produce such libraries have had specific use cases in mind. For example, one library is focused only on clients requesting and receiving a resource. Another library is focused on writing servers. Yet another library is focused on providing a REST interface. I consider these libraries to be high level. Beast aims to be a low level library. That is, to provide the common functionality that all these higher level libraries need. Specifically:  
  
1. A container that can hold a complete HTTP header and/or message  
2. Algorithms to serialize and deserialize HTTP/1 messages on streams  
  
That is what Beast intends to do. Normal application writers want many things that go beyond this. For example they want connection management, listening socket management, they want support for multiple transfer encodings, progress callbacks for big downloads, decoding of multipart/mime type, Accept/range support, the list could go on and on. But these are not features that I have planned for Beast, because what we need right now is to at least deliver the basics.  
  
Therefore, my goal for `basic_parser`, `parse_some`, and `async_parse_some` is to provide the minimum amount of API that allows for reading the message body into its container ("direct" reader algorithm), and allows callers to write read loops which can access the body information in order to implement any sort of thing, such as relaying, handle Expect: 100-continue, store the body in a file, decompress the body, or transform it incrementally (to name a few).

---

## Comment 108

> Username: vinniefalco  
> Created at: 2017-03-30 17:12:00 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290478204  

@mika-fischer That said, I'm greatly appreciative of the feedback because this is what I need in order to move forward and make further improvements. So if possible, I'd like to continue working with you on your example code to see if we can turn it into something that can be implemented. This was your proposal:  
  
```  
 1 beast::streambuf beast_buffer;  
 2 http::header_parser<true, http::fields> header_parser;  
 3 async_parse(socket, beast_buffer, header_parser, yield);  
 4 http::streaming_body_parser body(socket, beast_buffer, header_parser);  
 5 char buffer[4096];  
 6 beast::error_code ec;  
 7 while (true) {  
 8    size_t bytes_read = body.async_read(yield[ec]); // This will strip out chunk-encoding  
 9    if (ec) // ec will be eof when body is finished  
10        break; // error handling omitted here...  
11    // do something with block  
12 }  
```  
  
This doesn't quite fit into the current design of Beast.:  
  
* Line 4 We are constructing the parser with a dynamic buffer. The interface to the parser's `write` function does not use a dynamic buffer, just a `ConstBufferSequence`.  
  
* Line 4 We are constructing the parser with a socket. Currently, the algorithm to acquire data from a socket or stream is separate from the algorithm to do the parsing.  
  
* Line 5 So this implementation uses two different buffers? All of the requests thus far have been for a single buffer. Its not clear why you would want to have two separate buffers.  
  
* Line 8 the parser has an `async_read` member function? Again this mixes stream operations and parsing operations in the same class, something that I have purposefully avoided.  
  
*  Line 9 if the message indicates keep-alive then there will be no end of file. So there needs to be another way to terminate the loop. That's what `basic_parser::is_complete` is for.  
  
The way to move this forward is to figure out how to refactor your code example to fit into how the rest of the library's interfaces are structured.

---

## Comment 109

> Username: vinniefalco  
> Created at: 2017-03-30 19:16:14 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290515815  

@mika-fischer How about this?  
```  
template<bool isRequest,  
    class SyncReadStream, class BodyCallback>  
void  
doFixedRead(SyncReadStream& stream, BodyCallback const& cb)  
{  
    flat_streambuf buffer{4096, 4096}; // 4K size, 4K limit  
    header_parser<isRequest, fields> parser;  
    std::size_t bytes_used;  
    bytes_used = parse_some(stream, buffer, parser);  
    BEAST_EXPECT(parser.got_header());  
    buffer.consume(bytes_used);  
    do  
    {  
        bytes_used =  
            parse_some(stream, buffer, parser);  
        if(! parser.body().empty())  
            cb(parser.body());  
        buffer.consume(bytes_used);  
    }  
    while(! parser.is_complete());  
}  
```

---

## Comment 110

> Username: georgi-d  
> Created at: 2017-03-30 21:29:48 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290550623  

@vinniefalco The new interface looks much better. Thanks for all the effort. I have a question about your example for `doFixedRead`.   
  
Why is a `header_parser` used to parse both the headers and the body. I was under the impression that `header_parser` returns `is_complete() = true` after just the headers. All the previous examples required creating a regular parser from the header_parser before parsing the body.  
  
What would happen in this example if the headers are more than 4k long? Am I correct in assuming that `parse_some` would either throw an exception or report an error in `ec`?

---

## Comment 111

> Username: vinniefalco  
> Created at: 2017-03-30 21:44:56 UTC  
> Updated at: 2017-03-30 21:47:37 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290554175  

>@vinniefalco The new interface looks much better. Thanks for all the effort.   
  
Phew, glad to hear it! I spent many months of trial and error getting to here!  
  
>Why is a header_parser used to parse both the headers and the body. I was under the impression that header_parser returns is_complete() = true after just the headers.  
  
Yep, that's how it was. But then I realized, that `basic_parser` only consumes bytes, and `parse_some` accumulates bytes, until either the header is complete or an error occurs. So if you want to parse just the header, you call `parse_some` on any `basic_header`-derived class (`header_parser`, `message_parser`, or your own derived class). This does away with the "hack" in `parse` where it breaks out of the read loop (I had these members `pause` and `resume` which I also removed).  
  
You are correct that the name `header_parser` is not entirely accurate. It just means that the "captive object" is of type `http::header`. It is still capable of parsing the body, but its up to the caller to do something with those bytes since there is no **Body** type and no **Reader**. The `header_parser` uses the "indirect" Reader interface (see http://vinniefalco.github.io/stage/beast/beast/ref/Reader.html).  
  
>All the previous examples required creating a regular parser from the header_parser before parsing the body.  
  
You can still do that, if you want Beast to read directly from the socket, into the container represented by the **Body** type. This is an example of "direct" read.  
  
>What would happen in this example if the headers are more than 4k long? Am I correct in assuming that parse_some would either throw an exception or report an error in ec?  
  
`parse_some` doesn't know anything about limits, its up to the **DynamicBuffer** object to set the limit. `beast::flat_streambuf` lets you choose the initial size, and the maximum size, thus functioning as a de-facto limit on the size of a header or chunk header. However, `parse_some` will convert the `std::length_error` exception thrown by the **DynamicBuffer**'s `prepare` function (see http://vinniefalco.github.io/stage/beast/beast/ref/DynamicBuffer.html) into the `http::error::buffer_overflow` error code.  
  
I know its a little bit weird, and I hope that I can polish it up a tiny bit. But, I think I have captured all possible use-cases and as you know from your participation in this issue its not an easy task.  
  
---  
  
Just to be clear, this is the behavior of `parse_some` depending on `parse_state`:  
  
* `parse_state::header` or `parse_state::chunk_header`: will keep reading bytes until an error occurs or the entire header or chunk header is read in  
  
* `parse_state::body`, `parse_state::body_to_eof`, or `parse_state::chunk_body`: If data is in the dynamic buffer then it is copied to the body container for direct readers, else it invokes the `on_data` derived class callback for indirect parsers. It also sets the return value of `basic_parser::body()`. Then it returns. If there is no data in the dynamic buffer, then it performs at most one network call to `read_some` (or `async_read_some`) into either the body container for direct readers, or the dynamic buffer for indirect readers, then returns.

---

## Comment 112

> Username: mfojtak  
> Created at: 2017-03-31 07:59:03 UTC  
> Updated at: 2017-03-31 08:01:30 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290644021  

I agree that the latest example looks much better.  
There are still some questions:  
  
What is returned by` parser.body()`? Current chunk or accumulated body? The latter would kind of bring us back to square one - it would not work for large files, video streams etc.  
  
Semantics and naming - `header_parser` should be `http_parser`? Why `parse_some` and not `read_some` (to follow asio terminology)  
  
`parse_some` - why this is not a member method? Is there any reason for C-style function call?

---

## Comment 113

> Username: mika-fischer  
> Created at: 2017-03-31 08:22:55 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290648907  

@vinniefalco The exact API for my use-case is not important to me. On an abstract level the API should have the following properties:  
- No need to know about parser state (`.is_complete()` is fine, obviously, that's what I meant by EOF in my example)  
- No need to know about HTTP chunk encoding  
- Pull-style, i.e. the caller request bytes from the body and then the parser reads on the socket, not the other way around using callbacks.  
  
Your last example, if I understand it correctly, already has the first two properties, but not the third.  
  
However, @cbodley's example seems to have all properties. What do you think about his solution?  
  
> That is what Beast intends to do. Normal application writers want many things that go beyond this. For example they want connection management, listening socket management, they want support for multiple transfer encodings, progress callbacks for big downloads, decoding of multipart/mime type, Accept/range support, the list could go on and on. But these are not features that I have planned for Beast, because what we need right now is to at least deliver the basics.  
  
All of the things mentioned are somewhat optional, and will not be needed in all cases, and thus it's absolutely fine and most probably a wise decision to not tackle them. However, reading and writing the body (in a way that the caller chooses and without knowing about parser internals) _is_ IMHO one of the "basics" that is not so optional.  
  
In general, I think this is going in a good direction!

---

## Comment 114

> Username: georgi-d  
> Created at: 2017-03-31 08:35:57 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290651792  

@mika-fischer   
  
> Pull-style, i.e. the caller request bytes from the body and then the parser reads on the socket, not the other way around using callbacks.  
  
I actually agree with the first part of this but strongly disagree with the second part. I like the pull model where the user requests the next event but the parser should absolutely not read from the socket itself. The parser should be given a buffer with bytes and then events should be pulled from the parser until it returns that it needs more data. Then the process should repeat until the parser indicates that the message has completed.   
  
The application should be free to obtain the bytes it passes to the parser in any way it wants and the parser should be able to receive them. Some possible sources that come to mind: openssl BIO objects, static strings, generator methods, message bus etc. The application should not need to wrap those in a stream/socket abstraction just to pass it to the parser.  
  
This is why `parse_some` is a free function because it is a place where the socket abstraction and the parser abstraction are combined  but it is independent from either of them.

---

## Comment 115

> Username: vinniefalco  
> Created at: 2017-03-31 09:35:18 UTC  
> Updated at: 2017-03-31 09:35:36 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290666390  

>What is returned by parser.body()? Current chunk or accumulated body?  
  
The current chunk, as a `boost::string_ref`.  
  
>Semantics and naming - header_parser should be http_parser?  
  
`header_parser` is named thusly because the object it manages has type `http::header`.  
  
I think `http_parser` is not such a great name, because qualified with the namespace it would read `http::http_parser which doesn't look clean.  
  
>Why parse_some and not read_some (to follow asio terminology)  
  
`read_some` just reads into a buffer. While `parse_some` does a bit more, so I think the name better captures the spirit of what the function is doing.  
  
>parse_some - why this is not a member method? Is there any reason for C-style function call?  
  
You're asking me to explain C++ design principles! Herb Sutter does a much better job of that than me, start here:  
http://www.gotw.ca/gotw/084.htm  
https://en.wikipedia.org/wiki/Open/closed_principle

---

## Comment 116

> Username: vinniefalco  
> Created at: 2017-03-31 09:44:48 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-290668410  

>I was able to implement a simple streaming_body class that works with the existing interfaces to accomplish this in a way that I thought was pretty elegant. Is something like this sufficient?  
  
I love it! Your branch seems a bit out of date but this is exactly what I was talking about Beast being a low-level library. So what you have done here is taken the pieces that the library provides and rolled a solution. Its great that the problem can be solved multiple ways, it means that the current interfaces are good!  
  
>* Pull-style, i.e. the caller request bytes from the body and then the parser reads on the socket, not the other way around using callbacks.  
  
>Your last example, if I understand it correctly, already has the first two properties, but not the third.  
  
My last example could be modified to return the `boost::string_ref` instead. The use of the callback was expository.  
  
>However, @cbodley's example seems to have all properties. What do you think about his solution?  
  
I think its a great solution! I think it validates the current design. Its not something that I would include in the library. But I will have to make sure that the **Reader** concept `prepare` function is allowed to return a smaller buffer than requested, to make it work.

---

## Comment 117

> Username: vinniefalco  
> Created at: 2017-06-07 17:56:29 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-306874799  

@georgi-d @harrishancock @cbodley @mfojtak @mika-fischer @e-fominov  
  
There have been a lot of changes to the library, and I think I have addressed all of the use-cases and proposals in this issue. Would you folks mind please providing some feedback, does the library suit your needs now? Is there anything missing? What would you change? Thanks!

---

## Comment 118

> Username: vinniefalco  
> Created at: 2017-06-07 17:58:17 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-306875322  

Shout out to @cbodley I shamelessly adopted your beautiful hack of customizing the Body to support manual buffers, its now part of the library:  `buffer_body` https://github.com/vinniefalco/Beast/blob/ac5bc4f62c318812104ad2cef13b2d3a19ada8bb/include/beast/http/buffer_body.hpp#L21  
  
It works for both reading and writing, and there are compiling examples in the documentation on how to make it work, including implementing an HTTP relay using a fixed buffer!

---

## Comment 119

> Username: georgi-d  
> Created at: 2017-06-07 20:14:00 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-306911385  

Thanks for all the work!  
The changes seem to address all the use cases I had in mind and I will start trying to integrate and use beast in my project in the coming weeks. I will have a much better understanding if all cases are covered then.

---

## Comment 120

> Username: harrishancock  
> Created at: 2017-06-07 20:32:23 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-306915996  

@vinniefalco Beast has been awesome for me! I like the new `flat_buffer` and `multi_buffer` names, and find both types useful in my code. `flat_buffer` in particular comes in handy when I have to interface with some of my old APIs that still expect contiguous buffers.

---

## Comment 121

> Username: vinniefalco  
> Created at: 2017-06-08 20:09:25 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-307213358  

@cbodley @georgi-d @mfojtak @mika-fischer Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!

---

## Comment 122

> Username: vinniefalco  
> Created at: 2017-06-12 03:09:50 UTC  
> Url: https://github.com/boostorg/beast/issues/154#issuecomment-307680698  

This was a seriously epic thread, thanks to everyone for helping out with the tough questions. I'm going to retire this issue once and for all!!!
