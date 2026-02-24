# #2796 - Ergonomics of error list in `http::async_read` and `http::async_read_header` [Closed]

> Username: ecorm  
> Created at: 2024-01-11 15:55:57 UTC  
> Updated at: 2024-01-11 16:31:59 UTC  
> Closed at: 2024-01-11 16:31:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2796  

### Version of Beast  
  
Boost 1.84  
  
### Description  
  
In the documentation for [`http::async_read`](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html), it lists `error::partial_message` as one of the possible errors. But that does not make sense, as the description says  
  
> Read a **complete** message  asynchronously from a stream using a parser.  
  
If the function reads a complete message (or fails), how could it possibly ever emit an `error::partial_message` error?  
  
Unless I misunderstood something, I think this is a copy-paste error.  
  
I also found `error::partial_message` listed under `http::async_read_header` where it's supposed to read a **complete** header.

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-11 16:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2796#issuecomment-1887492138  

The `partial_message` error is a parser error that occurs when the end of the file is reached in the middle of parsing a message. Are you suggesting that it doesn't seem like an error?

---

## Comment 2

> Username: ecorm  
> Created at: 2024-01-11 16:24:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2796#issuecomment-1887520511  

I missed the sentence above the error bullet points that says "If the end of file error is received while reading from the stream, then the error returned from this function will be: "  
  
When consulting a function's documentation, when I see a bullet list of error codes, my eyes immediately jump to them, and it's just natural for me to miss the context above those bullet points if it's buried in a long paragraph.  
  
There should be a paragraph break before the "If the end of file error is received while reading from the stream, then the error returned from this function will be: " sentence.  
  
In other words, it should appear like this:  
  
> This operation is implemented in terms of zero or more calls to the next layer's `async_read_some` function, and is known as a _composed operation_. The program must ensure that the stream performs no other reads until this operation completes. The implementation may read additional bytes from the stream that lie past the end of the message being read. These additional bytes are stored in the dynamic buffer, which must be preserved for subsequent reads.  
>  
> If the end of file error is received while reading from the stream, then the error returned from this function will be:  
>  
> - `error::end_of_stream` if no bytes were parsed, or  
> - `error::partial_message` if any bytes were parsed but the message was incomplete, otherwise:  
> - A successful result. The next attempt to read will return error::end_of_stream

---

## Comment 3

> Username: ashtum  
> Created at: 2024-01-11 16:28:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2796#issuecomment-1887528268  

Yeah, this issue has already been addressed in #2793. The latest version of the documentation, does not have this problem: https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html

---

## Comment 4

> Username: ecorm  
> Created at: 2024-01-11 16:31:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2796#issuecomment-1887534259  

I was just going to comment that the paragraph break I suggested was already in the [source code](https://github.com/boostorg/beast/blob/9d05ef58d19672d91475e6be6f746d9bdb031362/include/boost/beast/http/read.hpp#L369).  
  
I will consult the master branch for the documentation until the next release. Thank you for the prompt responses.  
  
Closing as duplicate of #2792 .
