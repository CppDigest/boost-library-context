# #1304 - POST request with file_body type [Closed]

> Username: JanVerschaeren  
> Created at: 2018-11-15 16:06:23 UTC  
> Updated at: 2020-04-06 17:53:08 UTC  
> Closed at: 2018-11-17 12:51:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1304  

Hi,   
  
I am new to boost beast and i'm playing around a bit with some examples.  
I am trying to Post a request with a file_body type.  
In the code below, target, version and host are variables like in the getting started example.  
  
This is my code:  
  
  
`  
        //opening a file  
        boost::beast::error_code e_c;  
  
        http::file_body::value_type body;  
  
        std::string path = "resources/my_image.png";  
  
        body.open(path.c_str(), boost::beast::file_mode::read, e_c);  
  
        // posting the request using file_body  
        http::request<http::file_body> req;  
        req.method(http::verb::post);  
        req.target(target);  
        req.version(version);  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
  
        req.body() = std::move(body);`  
  
  
  
I get this compiler error:   
  
error: binding ‘const boost::beast::http::message<true, boost::beast::http::basic_file_body<boost::beast::file_posix>, boost::beast::http::basic_fields<std::allocator<char> > >’ to reference of type ‘boost::beast::http::serializer<true, boost::beast::http::basic_file_body<boost::beast::file_posix>, boost::beast::http::basic_fields<std::allocator<char> > >::value_type& {aka boost::beast::http::message<true, boost::beast::http::basic_file_body<boost::beast::file_posix>, boost::beast::http::basic_fields<std::allocator<char> > >&}’ discards qualifiers  
     serializer<isRequest, Body, Fields> sr{msg};   
  
  
But my questions is:  Can you actually put a POST request with a file_body type?  
If so what is the correct way to do it?  
And how do i read this error, so i can maybe fix the problem myself?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-15 16:41:13 UTC  
> Updated at: 2018-11-15 16:42:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1304#issuecomment-439106087  

You should be able to POST with any body type, including files. However, to serialize a file body you need a writable messages. This is because the serialization operation needs to advance the file pointer (stored in the body) which is a non-const operation. It looks like the error is coming from code that you did not provide, but I think I can guess what happened. You are using `http::write` or `http::async_write` which accepts the complete message as a parameter. These functions only work on const messages. Instead, you need to construct an `http::serializer` and use the version of write or async_write which accepts the serializer. The documentation provides an explanation and examples of how to do this.  
  
I should point out that I am not entirely happy about how the body types and serialization turned out, and I will certainly be doing a big refactoring which changes how it works in order to make this problem go away, among other things. It is likely that these will be breaking changes.

---

## Comment 2

> Username: JanVerschaeren  
> Created at: 2018-11-16 09:36:07 UTC  
> Updated at: 2018-11-16 09:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1304#issuecomment-439335905  

First of all: Thanks for the quick and constructive answer!  
  
I did indeed used a `http::write` with parameters: `tcp::socket` (like in the tutorial) and `http::request`.  
I did not need to alter the above code.  
  
Now i changed the `http::write(socket, req)` to:  
        
        `http::request_serializer<http::file_body,  http::fields> sr{req};`  
  
        `http::write(socket, sr);`  
  
So now it compiles and successfully sends the data to a server that accepts a post request.  
(Should i close this topic? If so, could you close this topic for me because i don't know how :p )  
  
  
I do still have a few questions however, i have been looking through the documentation and can't seem to find it (or understand it).   
So:   
- How does this serializer work?  
Does it convert the data of the opened file to a string and then sends a post request with a string_body type? Or does it just sends the raw data without "looking" into the file and converting it to a string?  
  
- Is there a way to send the data without loading it into memory? I am assuming here that i do in fact load the file's content into the `http::file_body::value_type` object.  
  
- It maybe the answer to the previous question, but how do i use a `websocket::stream` instead of a `tcp::socket`?  
I looked at the "how to create streams" tutorial and when i create a stream, preform the handshake, etc... I then try to do the `http::write(my_stream, my_serializer);` i get an assertion error saying i don't have a `sync_write_stream`. How can i make such a stream?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-16 14:16:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1304#issuecomment-439406151  

> How does this serializer work?  
It reads the file one 4KB piece at a time and sends each piece:  
https://github.com/boostorg/beast/blob/1ca690db0615a170c155683968071ee9eb609bc0/include/boost/beast/http/basic_file_body.hpp#L232  
  
On Windows it uses `::TransmitFile` which is kernel-optimized.  
  
>Is there a way to send the data without loading it into memory?  
  
Yes, this happens automatically when using `file_body`.  
  
> how do i use a `websocket::stream` instead of a `tcp::socket`  
  
A websocket stream is not the same as a socket. WebSocket is a message-oriented protocol while TCP/IP is a byte-stream oriented protocol. So you can't really use the HTTP algorithms on a websocket stream. But why would you want to send an HTTP message in a websocket message anyway?

---

## Comment 4

> Username: JanVerschaeren  
> Created at: 2018-11-16 14:28:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1304#issuecomment-439409698  

I didn't want to send http messages in a websocket message.  
It was suggested to me to stream a file instead of loading it into memory then sending it.  
  
When i looked up "boost beast stream" i found the websocket stream and thought it was the same.  
So i guess i was doing it right all along!  
  
Thanks for the clear answers again!
