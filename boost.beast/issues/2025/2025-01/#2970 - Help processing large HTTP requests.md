# #2970 - Help processing large HTTP requests [Closed]

> Username: korydraughn  
> Created at: 2025-01-10 16:39:09 UTC  
> Updated at: 2025-04-15 12:59:53 UTC  
> Closed at: 2025-04-15 12:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2970  

I'm using the following:  
- Boost 1.81  
- Clang 13  
  
I have a server which expects the full HTTP request to be sent before processing it. This is fine for the majority of cases, except when handling large file uploads.  
  
If possible, I'd like for the server to not wait for the whole request to be uploaded.  
  
Does the Beast library provide a way to work with HTTP requests in an incremental manner?  
  - i.e. Process parts of the request as they arrive.  
  - If so, does Beast still detect errors while doing this?  
  
Are there examples demonstrating this with the async_read operations?

---

## Comment 1

> Username: ashtum  
> Created at: 2025-01-10 17:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2970#issuecomment-2583277217  

If you want to read the body piece by piece, you can use [http::buffer_body](https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html). Here is an example: [HTTP Relay](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/more_examples/http_relay.html).  
You can also use [http::file_body](https://www.boost.org/doc/libs/1_87_0/libs/beast/doc/html/beast/ref/boost__beast__http__file_body.html) if you know the message needs to be written to a file beforehand.

---

## Comment 2

> Username: sehe  
> Created at: 2025-01-10 17:34:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2970#issuecomment-2583326456  

What @ashtum said. Besides, for advanced topics (like filtering through an external process) see e.g. https://stackoverflow.com/questions/61787334/how-to-read-data-from-internet-using-muli-threading-with-connecting-only-once/61809655#61809655

---

## Comment 3

> Username: korydraughn  
> Created at: 2025-01-10 21:40:22 UTC  
> Updated at: 2025-01-11 15:01:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2970#issuecomment-2584339846  

Use of a `http::buffer_body` aligns with what I was thinking.  
  
That means I have to write a multipart/form-data parser that can parse partial messages. The reason for that is due to the file upload interface not having the expected web-friendly interface _(which is intentional)_. For instance, the operation to upload a file looks like this:  
```  
curl <URL> <headers> \  
  -F 'op=write' \  
  -F 'lpath=/path/to/object/in/vfs' \  
  -F 'bytes=<binary_data>'  
```  
Notice the file contents is passed via the `bytes` parameter.  
  
The behavior of the server changes based on the `op` parameter, but I won't know what that is until that parameter appears.  
  
With that said, it's not clear to me how a `http::file_body` would work for this.

---

## Comment 4

> Username: ashtum  
> Created at: 2025-01-11 06:28:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2970#issuecomment-2585117354  

> With that said, it's not clear to me how a `http::file_body` would work for this.  
  
You can't use a `http::file_body` for that. `http::file_body` can only be used for receiving the whole body into a file.
