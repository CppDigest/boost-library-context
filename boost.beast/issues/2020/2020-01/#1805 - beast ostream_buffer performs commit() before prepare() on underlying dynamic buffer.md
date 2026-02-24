# #1805 - beast ostream_buffer performs commit() before prepare() on underlying dynamic buffer [Closed]

> Username: madmongo1  
> Created at: 2020-01-17 17:46:48 UTC  
> Updated at: 2020-01-30 14:07:49 UTC  
> Closed at: 2020-01-30 14:07:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1805  

### Version of Beast  
  
1.72.0  
  
### Steps necessary to reproduce the problem  
  
Create a lightweight proxy to hold a reference to a `boost::asio::dynamic_string_buffer` compiled with the following preprocessor symbol defined: `BOOST_ASIO_NO_DYNAMIC_BUFFER_V1`  
  
### All relevant compiler information  
  
gcc 9, fedora 31  
  
### To reproduce  
  
```  
std::string store;  
auto dynbuf = boost::asio::dynamic_buffer(store);  
auto os = boost::beast::ostream(LightWeightProxy(dynbuf));  
os << "foo";  
```  
  
### Outcome  
  
std library throws an exception, followed by segfault  
  
### Investigation  
  
`beast::ostream` creates a specialisation of `beast::detail::ostream_helper` which in turn references a `beast::detail::ostream_buffer`.  
  
`beast::detail::ostream_buffer` is derived from std::ostream and overrides member functions `overflow` and `sync`.  
  
The problem is that when handling the first character, `overflow` is called, which calls `sync`. `sync` then calls `commit(0)` on the underlying buffer. However, contrary to the requirements of asio v1 dynamic buffers, `prepare()` has not yet been called at this stage.  
  
Beast's dynamic buffers are resilient to this incorrect code sequence, but asio's V1 dynamic string buffer is not.  
  
### Solution  
  
Add a call to `b_.prepare(0);` in the constructor of `beast::detail::ostream_buffer`.  
  
Even though conceptually this is a no-op, it serves to satisfy the precondition of calling `b_.commit()`. The internal state of `asio::dynamic_string_buffer` is subtly modified even though the no difference is observed by calls to the `size()` member function by user code.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-30 14:07:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1805#issuecomment-580269518  

Fixed in PR #1806
