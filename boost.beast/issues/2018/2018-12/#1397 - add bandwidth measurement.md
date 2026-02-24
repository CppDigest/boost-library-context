# #1397 - add bandwidth measurement [Closed]

> Username: vinniefalco  
> Created at: 2018-12-30 08:50:34 UTC  
> Updated at: 2019-04-19 01:44:10 UTC  
> Closed at: 2019-04-19 01:44:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1397  

to `beast::basic_stream_socket`:  
  
Throughput, total sent/received, etc

---

## Comment 1

> Username: madmongo1  
> Created at: 2018-12-30 08:55:57 UTC  
> Updated at: 2018-12-30 08:56:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1397#issuecomment-450547369  

Two methods I would find useful:  
  
```  
/// @brief return total bytes sent through the socket since the last reset  
std::size_t bytes_sent() const;  
  
/// @brief reset the total-bytes-sent counter and return the current value    
std::size_t reset_bytes_sent(std::size_t = 0);    
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-02-23 03:11:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1397#issuecomment-466611002  

The bandwidth / traffic shaping feature in `basic_stream` is merged, but still needs documentation. The feature requested here, calculating throughput, can be implemented as a user-defined _RatePolicy_ associated with the stream. This should be provided as a complete example in the documentation.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-04-19 01:44:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1397#issuecomment-484741800  

This is done https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/using_io/layered_streams/counted_stream_example_new.html
