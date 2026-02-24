# #74 - boost-1.66 regression in ssl socket wrapper [Closed]

> Username: arvidn  
> Created at: 2017-12-14 09:40:39 UTC  
> Updated at: 2017-12-14 11:16:56 UTC  
> Closed at: 2017-12-14 11:16:56 UTC  
> Url: https://github.com/boostorg/asio/issues/74  

I'm wrapping my custom socket type in the [boost::asio::ssl::stream](https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/stream.hpp#L64) wrapper. Building with boost-1.66-rc1 I get a compilation error caused by the way the ssl wrapper use ``read_some()``.  
  
According to the [documentation](http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/basic_stream_socket/read_some.html)  the function takes a *MutableBufferSequence*, defined [here](http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/MutableBufferSequence.html)  
 to have a ``const_iterator`` type and ``begin()`` and ``end()`` functions, to iterate the buffer sequence.  
  
However, in the boost-1.66-rc1 version of asio's ssl stream wrapper, it calls ``read_some()`` ([here](https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/io.hpp#L44)) with a ``boost::asio::mutable_buffer`` (i.e. not a sequence). It passes ``core.input_buffer_`` which is defined [here](https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/stream_core.hpp#L123).  
  
As far as I can tell, ``core.input_buffer`` should be of type ``boost::asio::mutable_buffers_1``. Making this change makes my program build again.

---

## Comment 1

> Username: arvidn  
> Created at: 2017-12-14 11:16:56 UTC  
> Url: https://github.com/boostorg/asio/issues/74#issuecomment-351682515  

explained here: https://github.com/boostorg/asio/pull/75#issuecomment-351674308
