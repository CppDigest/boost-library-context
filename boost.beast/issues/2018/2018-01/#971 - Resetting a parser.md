# #971 - Resetting a parser? [Closed]

> Username: chrisics  
> Created at: 2018-01-09 12:26:13 UTC  
> Updated at: 2025-02-22 20:33:31 UTC  
> Closed at: 2018-01-09 15:01:44 UTC  
> Url: https://github.com/boostorg/beast/issues/971  

When a server is done processing a request, it wants to parse a new request from the socket.   
  
We need to provide a new parser for each new request. In asynchronous mode, that means allocating heap memory.   
It would be more convenient to re-use a parser as a member variable and reset it state for new read operation.   
  
Or did I miss something? Thank you.  
  
    void do_read()  
    {  
        // Read a new request  
        **parser_.reset();**    
   
        http::async_read_header(stream_, buf_, parser_,  
                            boost::asio::bind_executor(strand_,  
                                                       std::bind(&session::on_read_header,  
                                                                 this,  
                                                                 std::placeholders::_1,  
                                                                 std::placeholders::_2)));  
    }

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-09 14:55:36 UTC  
> Updated at: 2018-01-09 14:56:54 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-356306911  

Adding `parser::reset` would cause restrictions on the constructors of `message`, which are open ended because they are user definable (for example, message constructor arguments forwarded to the body member).  
  
The solution is to store the parser in a `boost::optional` and recreate it using `optional::emplace` as needed.

---

## Comment 2

> Username: jeremiahar  
> Created at: 2022-06-25 15:50:09 UTC  
> Updated at: 2022-06-25 15:53:11 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-1166314002  

Another option is to manually call the destructor and use placement new. This avoids the overhead of optional when you don't really need it.  
  
```c++  
using T = boost::beast::http::request_parser<boost::beast::http::empty_body>;  
m_Parser.~T();  
new (&m_Parser) T();  
```  
  
Disclaimer: Make sure that your T matches :)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-25 16:00:08 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-1166315573  

there's no overhead to optional...

---

## Comment 4

> Username: jeremiahar  
> Created at: 2022-06-25 16:11:56 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-1166317538  

I was referring to the penalty to the sizeof optional<T> vs T. There has to be at least one extra bool of state to determine whether the T needs to be destructed or not. This can be as high as 8 bytes due to padding (you can check sizeof(void*) vs sizeof(std::optional<void*>).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-06-25 16:14:41 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-1166317959  

yeah that's true

---

## Comment 6

> Username: steve-numeus  
> Created at: 2023-05-16 09:42:15 UTC  
> Updated at: 2023-05-16 13:32:00 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-1549337342  

If I am using a `response_parser` with a `string_body`, even if I use an `optional`, it will still heap allocate for the string body, correct?  
  
```c++  
beast::flat_buffer _read_buf;  
std::optional<http::response_parser<http::string_body>> _parser;  
```  
  
In my read function:  
  
```c++  
_parser.emplace();  
_parser->body_limit(std::numeric_limits<std::size_t>::max());  
  
http::async_read(_stream, _read_buf, *_parser, _read_cb);  
```  
  
I will read the entire response in the callback, and therefore on the next read I can reuse whatever internal buffers etc have been allocated in the previous read.  
  
What is the most efficient way to do this, and avoid heap allocations during the read operation (or at least, for reads the same size or smaller than the largest read this far)?  
  
Perhaps a different body type, or a way to pass a buffer to the parser?  
  
---  
  
**Edit:**  
  
In the end I created a buffer which conforms to the `DynamicBuffer` concept requirements.  
  
Inside I used a `boost::container::small_vector` which provides an inline storage and can dynamically allocate if required.  
  
I found it quite surprising that there isn't something like this already.

---

## Comment 7

> Username: Spongman  
> Created at: 2025-02-22 20:33:30 UTC  
> Url: https://github.com/boostorg/beast/issues/971#issuecomment-2676383329  

also...  
```  
	std::destroy_at(&parser_);	// c++17  
	std::construct_at(&parser_);	// c++20  
```
