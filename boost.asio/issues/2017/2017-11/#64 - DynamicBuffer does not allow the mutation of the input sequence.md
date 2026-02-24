# #64 - DynamicBuffer does not allow the mutation of the input sequence [Closed]

> Username: djarek  
> Created at: 2017-11-13 23:57:47 UTC  
> Updated at: 2019-03-03 17:42:22 UTC  
> Closed at: 2019-03-03 17:42:22 UTC  
> Url: https://github.com/boostorg/asio/issues/64  

DynamicBuffer does not allow the mutation of the input sequence, which is useful for protocols, which require it to be modified to extract payloads. While users may just copy the data, it might be useful to consider allowing the execution of such algorithms in place, for performance reasons.  
  
This would require adding a non-const qualified overload of ```data()``` to the DynamicBuffer concept:  
```cpp  
mutable_buffer_type data();  
const_buffer_type data() const;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-14 00:00:48 UTC  
> Updated at: 2017-11-14 00:01:17 UTC  
> Url: https://github.com/boostorg/asio/issues/64#issuecomment-344100159  

I agree in principle that it would be nice if the input area was modifiable but I'm not sure that this is the right way to do it.  
  
The problem is that `mutable_buffers_type` may not be an appropriate type to represent the input area as a mutable buffer sequence. For example, `basic_multi_buffer::const_buffers_type` and `basic_multi_buffer::mutable_buffers_type` cannot be used interchangeably (https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/impl/multi_buffer.ipp#L124)  
  
Note the difference between the implementations of `basic_multi_buffer::const_buffers_type::const_iterator` and `basic_multi_buffer::mutable_buffers_type::const_iterator`:  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/impl/multi_buffer.ipp#L225  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/impl/multi_buffer.ipp#L342

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-03-02 13:39:30 UTC  
> Url: https://github.com/boostorg/asio/issues/64#issuecomment-468921530  

Beast will introduce the _MutableDynamicBuffer_ concept, but I'll wait until after Chris resolves the `DynamicBuffer&&` issue raised in P1100R0. Note that all the Beast dynamic buffers support modifiable input areas, in the fashion you described.
