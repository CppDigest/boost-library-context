# #993 - outdated concept: BufferSequence [Closed]

> Username: qwertzui11  
> Created at: 2018-01-22 12:33:42 UTC  
> Updated at: 2018-01-23 15:07:51 UTC  
> Closed at: 2018-01-22 14:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/993  

### Version of Beast  
boost 1.66.0  
  
### Steps necessary to reproduce the problem  
i got a custom write stream  
```  
struct writer {  
  std::size_t write_some(boost::asio::const_buffer buffer);  
  std::size_t write_some(boost::asio::const_buffer buffer, boost::system::error_code&);  
};  
```  
with asio the following works:  
```  
writer writer_;  
std::vector<char> buffer;  
boost::asio::write(writer_, boost::asio::buffer(buffer));  
```  
with beast the following does not compile  
```  
writer writer_;  
boost::beast::http::serializer<...> serializer;  
boost::beast::http::write_header(writer_, serializer);  
```  
with the beautiful error  
```  
static_assert(is_sync_write_stream<SyncWriteStream>::value,  
        "SyncWriteStream requirements not met");  
```  
after inspecting the code of `is_sync_write_stream` it seems, beast uses the old ConstBufferSequence concept for `write_some` used/needed by SyncWriteStream.  
NEW: http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/ConstBufferSequence.html  
OLD: http://www.boost.org/doc/libs/1_65_0/doc/html/boost_asio/reference/ConstBufferSequence.html  
  
### All relevant compiler information  
ubuntu 17.04 64bit  
gcc6.4  
additional compile flags: -DBOOST_ASIO_NO_DEPRECATED

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-22 14:00:41 UTC  
> Updated at: 2018-01-22 14:01:18 UTC  
> Url: https://github.com/boostorg/beast/issues/993#issuecomment-359431170  

Beast uses Asio's `is_const_buffer_sequence` and `is_mutable_buffer_sequence` type checks. The problem is that your `writer` does not meet the requirements of **SyncWriteStream**:  
http://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/SyncWriteStream.html  
  
Your `writer::write_some` may only be passed a `boost::asio::const_buffer`. It cannot be passed **any** type of ConstBufferSequence.  
  
The declarations for your class should look like this:  
```  
struct writer  
{  
  template<class ConstBufferSequence>  
  std::size_t write_some(ConstBufferSequence const& buffers);  
  
  template<class ConstBufferSequence>  
  std::size_t write_some(ConstBufferSequence const& buffers, boost::system::error_code&);  
};  
```  
  
The call to `boost::asio::write` compiles because Asio does not perform type checking on the stream parameter. None of Beast's stream algorithms can work with a write stream that only supports writing a single contiguous buffer, implied by your signature. They require a write stream that fully supports the concept.  
  
Hope this helps!

---

## Comment 2

> Username: qwertzui11  
> Created at: 2018-01-22 14:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/993#issuecomment-359433305  

Thanks a lot for your answer.  
I am doing this that way, because I want `write_some` to be `virtual` and override by a sub-class. That way I want to erase ssl vs non-ssl stream. I guess that's not the right way. I'll try to solve it with `variant` and some `bool is_ssl`. Or by passing the `parser` instance to the virtual class.  
Thx again

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-22 14:15:04 UTC  
> Url: https://github.com/boostorg/beast/issues/993#issuecomment-359435022  

You can create a type-erased buffer sequence (at the expense of some memory allocations).  
  
The Beast example **advanced-server_flex** shows how to write code which works for both SSL and non-SSL without resorting to type erasure:  
  
https://github.com/boostorg/beast/blob/develop/example/advanced/server-flex/advanced_server_flex.cpp

---

## Comment 4

> Username: qwertzui11  
> Created at: 2018-01-23 14:29:04 UTC  
> Url: https://github.com/boostorg/beast/issues/993#issuecomment-359807297  

I read the example and I'm quite excited about the beautiful "Curiously recurring template pattern".  
However how would you design a collection of websocket streams (some with and some without ssl). How would you store these in a std::unordered_map? I guess not at all. I guess you'd use an std::enabled_shared_by_this + an event based approach. Right?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-01-23 15:07:51 UTC  
> Url: https://github.com/boostorg/beast/issues/993#issuecomment-359819282  

`shared_ptr<T>` is convertible to `shared_ptr<void>` with no loss of data. Also, I would probably store weak pointers in the map otherwise the connections can never die.
