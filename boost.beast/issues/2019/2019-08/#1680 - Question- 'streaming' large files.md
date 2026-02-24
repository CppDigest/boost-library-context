# #1680 - Question: 'streaming' large files [Closed]

> Username: redeemarr  
> Created at: 2019-08-12 23:18:27 UTC  
> Updated at: 2019-09-13 07:11:25 UTC  
> Closed at: 2019-09-13 07:11:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1680  

The task is to receive large files piece-by-piece over HTTP with specific portion size (say 4KB).  
Using raw asio it would be done this way:  
```C++  
char buffer[4096];  
async_read_some(io::buffer(buffer, sizeof(buffer)), ...  
```  
But I'm not sure how to implement this approach in Beast and which body and buffer types would be efficient for this task.  
  
I've tried using incremental reads with ```buffer_body```, ```flat_buffer``` and static array, as explained [here](https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html).  
It does work, every ```async_read_some``` call reads 512 bytes or less. But I found no way how to change this value.  
Also I'm confused about why we need extra static buffer, while we already have flat_buffer.  
  
Thanks.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-12 23:25:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1680#issuecomment-520630943  

The dynamic buffer used in HTTP parsing is an implementation detail, it has to copy the data to the buffer you provide. A future version of Beast will likely hide this implementation detail. If you want more than 512 bytes, make the static array larger, e.g. `char buf[1024];`.

---

## Comment 2

> Username: redeemarr  
> Created at: 2019-08-13 00:55:11 UTC  
> Updated at: 2019-08-13 02:19:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1680#issuecomment-520647917  

Ah, I didn't mention that static buffer was already 4k size. And ```async_read_some``` gave only 512 bytes. I suppose, this is OS and implementation specific.  
But when I changed ```async_read_some``` to just ```async_read```, it would work. 'Need buffer' error happened though, but it was expected since all the data couldn't fit into 4k.  
  
However, it doesn't work correctly sometimes, looks like some data is missing.  
For example (assuming using namespace ```boost::beast```):  
```C++  
flat_buffer m_buffer;  
http::response_parser<http::buffer_body> m_parser;  
std::vector<char> m_data(1024 * 1024 * 3); // large enough for the entire file (for debugging)  
```  
Reading the header first:  
```C++  
async_read_header(m_socket, m_buffer, m_parser, ...  
```  
And then reading the body:  
```C++  
auto& body = m_parser.get().body();  
body.data = m_data.data();  
body.size = m_data.size();  
http::async_read(m_socket, m_buffer, m_parser, [](boost::system::error_code const& err, std::size_t size)  
{  
  // ignore err == http::error::need_buffer  
  // Here I'm getting 'size' that sometimes is exactly the file size, but sometimes is slightly less  
  // Like few bytes (243 in this case) are still somewhere in parser?  
  // Edit: But m_data.size() - body.size is always the same and correct  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-13 15:00:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1680#issuecomment-520872423  

The way that `buffer_body` works, after the read, it will subtract from body.size the amount that it wrote, and it increments the body.data pointer by the number of bytes that it wrote. The code to do so is here:  
https://github.com/boostorg/beast/blob/6d614cf9a8c6d81258e309682621286bf9d74ec3/include/boost/beast/http/buffer_body.hpp#L133  
  
Are you taking this into account in your calculations?

---

## Comment 4

> Username: redeemarr  
> Created at: 2019-08-13 19:49:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1680#issuecomment-520982030  

Yes, calculating ```m_data.size() - body.size``` gives actual number of bytes received.  
In other words, ```capacity - free_space = bytes_received```.  
But the odd thing is value of ```std::size_t size```, passed to lambda, which should be equal to ```bytes_received```, but it does not. Not always.  
  
[This](https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/incremental_read.html) example uses synchronous calls and therefore does not have ```size``` that comes from the library, but calculates it instead. I use the same calculations for the same purpose in asynchronous call.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-09-12 20:36:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1680#issuecomment-530995857  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: redeemarr  
> Created at: 2019-09-13 07:11:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1680#issuecomment-531126029  

I suspect that ```size``` given by ```http::async_read``` call is not always correct, but it could be ignored and calculated instead.
