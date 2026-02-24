# #2860 - Is it possible to serialize the body directly to file from a request_parser<empty_body> [Closed]

> Username: meastp  
> Created at: 2024-04-30 13:52:07 UTC  
> Updated at: 2024-05-06 11:14:09 UTC  
> Closed at: 2024-05-06 05:01:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2860  

Hi,  
  
Thank you for making Boost.Beast - it's brilliant :)  
  
I'm serializing some of my requests that have a very large body for async processing. First, I'm parsing the headers with request_parser<empty_body>.  
  
What I do now is   
1. Continue processing the body into request_parser<file_body> and http::read(stream,buffer,parser), this is to limit memory usage  
2. Create http::request_serializer<http::file_body> from the request_parser.release().  
3. use boost::asio::stream_file file_stream and (in a loop) boost::beast::http::write_some(file_stream, serializer) to serialize _the whole request in a single file_ to disk  
  
Can this be done more efficiently than via file_body? I need to parse the headers, but I'd like to serialize the body in a single step if that's possible (or, at least, more efficiently than I currently do).

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-30 14:34:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2085508653  

You can utilize a [http::buffer_body](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html) and a fixed size buffer for that purpose, these examples might help:  
https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/more_examples/http_relay.html  
https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-04-30 17:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2086043701  

`file_body` can do exactly what you say

---

## Comment 3

> Username: ashtum  
> Created at: 2024-04-30 17:37:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2086185157  

> `file_body` can do exactly what you say  
  
If I understand correctly, they want to use `boost::asio::stream_file` and perform the write operation asynchronously.

---

## Comment 4

> Username: meastp  
> Created at: 2024-04-30 20:12:18 UTC  
> Updated at: 2024-04-30 20:22:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2087042235  

@vinniefalco @ashtum I don't need async write operation. If I understand file_body correctly, it is only the body content that is serialized to file, while I need the entire request serialized to a single file. Is my understanding of file_body correct?  
  
I'm trying the HTTP Relay example now, it does look like exactly what I'm after, but if there are simpler/better/more efficient solutions I'd love to learn about them :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-04-30 21:08:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2087327874  

You want your file to have the HTTP headers?

---

## Comment 6

> Username: meastp  
> Created at: 2024-05-06 05:01:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2095201302  

@vinniefalco Well, yes I do. Serializing the whole request to a single file for later processing instead of spreading it across multiple files makes it a bit easier to keep track. I might change it, but it's what I currently do. The HTTP Relay example was perfect - thanks @ashtum  :)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2024-05-06 11:14:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2860#issuecomment-2095771167  

No the relay example is not perfect. If you want the HTTP headers in the file then you should do the following:  
  
1. read the header  
2. write the header to a new file yourself  
3. read the rest of the body, appending into the new file using `file_body` with a single call to `http::async_read`  
  
Thanks
