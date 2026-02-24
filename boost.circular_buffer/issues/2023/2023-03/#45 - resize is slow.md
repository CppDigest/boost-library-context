# #45 - resize is slow [Open]

> Username: jeremy-coulon  
> Created at: 2023-03-31 09:50:08 UTC  
> Updated at: 2023-03-31 09:51:47 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/45  

Hello,  
  
Would it be possible to improve complexity of `circular_buffer<T>::resize(new_size, item)` in the case where `T` is scalar ?  
  
Currently, complexity is always linear.  
  
It would be nice to have constant complexity when `T` is a scalar type and no reallocation happens (ie. `new_size <= capacity()`).  
  
When we want to reduce size of buffer (ie. `new_size < size()`), you could call `erase_end(n)` internally which already implements an optimization for scalar types.  
  
When we want to increase size of buffer, you could optimize for scalar types by calling `std::memset()` at most 2 times.
