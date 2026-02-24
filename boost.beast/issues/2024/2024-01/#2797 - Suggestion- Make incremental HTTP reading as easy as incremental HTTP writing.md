# #2797 - Suggestion: Make incremental HTTP reading as easy as incremental HTTP writing [Closed]

> Username: ecorm  
> Created at: 2024-01-12 15:00:48 UTC  
> Updated at: 2024-01-12 17:16:59 UTC  
> Closed at: 2024-01-12 16:04:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2797  

Having just implemented a variant of the HTTP [incremental read example](https://www.boost.org/doc/libs/release/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html), I'm now implementing incremental HTTP writes and have noticed how much simpler and more intuitive it is for the latter.  
  
For incremental reading, you have to use a `request_parser<buffer_body>` and set the body's `data` and `size` attributes for every incremental read, which is clumsy and seems a little hackish to me:  
  
```c++  
request_parser<buffer_body> p;  
std::string actualBody;  
std::string bodyBuffer{512, '\0'};  
read_header(stream, buffer, p, ec);  
if(ec)  
    return;  
while(! p.is_done())  
{  
    p.get().body().data = &bodyBuffer.front();  
    p.get().body().size = bodyBuffer.size();  
    read(stream, buffer, p, ec);  
    if(ec == error::need_buffer)  
        ec = {};  
    if(ec)  
        return;  
    actualBody.append(bodyBuffer, bodyBuffer.size() - p.get().body().size);  
}  
  
// We can't use p.get().body() to access the complete body, but must instead use actualBody.  
```  
  
Notice the need for a separate `std::string actualBody` object because I am forced to use `request_parser<buffer_body>` instead of `request_parser<string_body>`.  
  
Compare that to incremental HTTP writing where the partial write size can be bounded via `serializer::limit`:  
  
```c++  
response_serializer<string_body> sr{m};  
sr.limit(512);  
  
do  
{  
    write_some(stream, sr);  
}  
while(! sr.is_done());  
```  
  
It seems to me there should be a `parser::limit` function to make it symetrical with `serializer::limit`. Incremental HTTP reading should be as simple as this:  
  
```c++  
request_parser<string_body> p;  
read_header(stream, buffer, p, ec);  
if(ec)  
    return;  
p.limit(512); // Not to be confused with body_limit, which is for the total body size  
while(! p.is_done())  
{  
    read_some(stream, buffer, p, ec);  
}  
  
// p.get().body() can be used to access the entire reassembled body  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-12 15:19:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889492932  

Could you please elaborate on how you can achieve the same functionality with the code in the last example? Do you want to call `clear` on `string_body` if you only need to consume and discard the body?

---

## Comment 2

> Username: ecorm  
> Created at: 2024-01-12 15:49:55 UTC  
> Updated at: 2024-01-12 15:53:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889543240  

> Do you want to call `clear` on `string_body` if you only need to consume and discard the body?  
  
That could work if one doesn't mind the temporary memory usage. Perhaps, in those instances where one wants to discard the body, there could be a modifier in the parser that sets it to "discard mode". Or perhaps some new `flush_body` and `async_flush_body` functions?  
  
I should clarify that I'm not stuck and the approach provided in the incremental read example works for me. It's just that I noticed how much "cleaner" incremental writes are and wondered if the Beast API could be enhanced so that it's just as tidy for incremental reads.  
  
As the issue title suggests, this is suggestion for improvement and not a bug. Do with it as you wish. I will not feel offended if you don't deem it worthy of implementation and close this issue.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-01-12 15:56:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889555155  

You are right that the interface for incremental reading is a bit obtuse in Beast. This is a consequence of some design choices that weren't great. But it was the best I could do at the time. Changing this would require quite a bit of rethinking about how Beast works and I'm not so sure I want to invest in that since Beast is effectively in maintenance mode.

---

## Comment 4

> Username: ecorm  
> Created at: 2024-01-12 16:04:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889567889  

> This is a consequence of some design choices that weren't great. But it was the best I could do at the time.  
  
I completely understand, having been down that road myself. Despite its warts, this library is still 10x better then what I could have written myself and has saved me countless hours of reinventing the wheel. Kudos to you and the other maintainers!  
  
> since Beast is effectively in maintenance mode  
  
I was not aware of that (or I forgot). Since the library is in maintenance mode, I will close this issue, and a record will be kept for posterity in case a new Beast 2 is developed, or someone else writes a new C++ library that uses this one for inspiration.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-01-12 16:07:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889571898  

"Beast 2" would be these:  
https://github.com/cppalliance/buffers/  
https://github.com/cppalliance/http_proto/  
https://github.com/cppalliance/http_io/

---

## Comment 6

> Username: ecorm  
> Created at: 2024-01-12 16:13:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889581605  

> "Beast 2" would be these:  
  
I ought to check those out and provide feedback now, instead of complaining years after their release like I'm doing now for Beast. :grin:

---

## Comment 7

> Username: vinniefalco  
> Created at: 2024-01-12 17:16:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2797#issuecomment-1889675331  

They are still in development though, and probably will be for at least 2 years
