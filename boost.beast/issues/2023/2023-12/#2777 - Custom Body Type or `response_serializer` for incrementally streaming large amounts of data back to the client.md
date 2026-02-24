# #2777 - Custom Body Type or `response_serializer` for incrementally streaming large amounts of data back to the client [Open]

> Username: korydraughn  
> Created at: 2023-12-21 21:41:39 UTC  
> Updated at: 2023-12-24 16:12:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2777  

## Question  
  
I'm using Beast 1.81 with Clang 13.0.0 to present a distributed data management API over HTTP.  
  
I've been reading a lot of the documentation and it's not clear to me what the recommended solution is for streaming large amounts of data back to the client. The pages that look like they are close to what I want are:  
- https://www.boost.org/doc/libs/1_81_0/libs/beast/doc/html/beast/using_http/serializer_stream_operations.html  
- https://www.boost.org/doc/libs/1_81_0/libs/beast/doc/html/beast/using_http/custom_body_types.html  
  
At the moment, my application presents something similar to POSIX read. That is, it forces the client to make multiple read requests to the HTTP server. This isn't ideal, but is good enough for a first pass _(I'm still getting familiar with Beast)_.  
  
You can view the code in question here:  
- https://github.com/irods/irods_client_http_api/blob/38d67aa5ab798952450dc4429c20d82586a375e8/src/endpoints/data_objects/impl.cpp#L417-L420  
  
The application has a background thread pool for long running operations. I want to use that to incrementally stream the data back.  
  
Q. Does a custom body type make sense here? Or would a `response_serializer` be better?  
Q. Is `transfer-encoding: chunked` automatically supported by custom body types?  
Q. Are there examples which demonstrate how to do this asynchronously?  
  
BTW, thanks for the awesome library.

---

## Comment 1

> Username: ashtum  
> Created at: 2023-12-22 10:40:14 UTC  
> Updated at: 2023-12-22 11:02:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2777#issuecomment-1867527811  

You can utilize a [http::buffer_body](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html) to send the response part by part. This example might help you: https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/more_examples/send_child_process_output.html.  
  
If you are reading from a file, you might want to consider using [http::file_body](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__file_body.html), which performs all these steps automatically.  
  
Alternatively, you can create a custom body type that reads from a stream into a fixed-size buffer and returns the corresponding buffers until it reaches the end of the stream.

---

## Comment 2

> Username: korydraughn  
> Created at: 2023-12-22 13:20:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2777#issuecomment-1867681906  

Thanks. I'll give that a try.

---

## Comment 3

> Username: korydraughn  
> Created at: 2023-12-24 16:12:07 UTC  
> Updated at: 2023-12-24 16:12:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2777#issuecomment-1868550380  

The **send child process output** example was exactly what I needed.  
  
Perhaps adding an async example that demonstrates the same thing would be helpful to others. Mostly to show how to manage the lifetime of the `response_serializer` across async calls.
