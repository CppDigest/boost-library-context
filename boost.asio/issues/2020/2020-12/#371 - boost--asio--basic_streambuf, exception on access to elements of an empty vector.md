# #371 - boost::asio::basic_streambuf, exception on access to elements of an empty vector [Closed]

> Username: artem-kamyshev  
> Created at: 2020-12-24 13:35:37 UTC  
> Updated at: 2020-12-30 01:15:07 UTC  
> Closed at: 2020-12-30 01:15:06 UTC  
> Url: https://github.com/boostorg/asio/issues/371  

The following code takes address of a buffer and call `operator[]` to get the address: `&buffer_[0]`. For an empty vector this is an undefinde behaviour by standard.  
  
https://github.com/boostorg/asio/blob/933e9373d2814c7e823c32cd6c242eb7222906c9/include/boost/asio/basic_streambuf.hpp#L312  
  
This might usually work, but in case of debug iterators enabled in Visual Studio compiler, it throws an exception, see file `vector` in a line `_DEBUG_ERROR("vector subscript out of range");`:  
  
```  
  
	reference operator[](size_type _Pos)  
		{	// subscript mutable sequence  
 #if _ITERATOR_DEBUG_LEVEL == 2  
		if (size() <= _Pos)  
			{	// report error  
			_DEBUG_ERROR("vector subscript out of range");  
			_SCL_SECURE_OUT_OF_RANGE;  
			}  
```

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:15:05 UTC  
> Url: https://github.com/boostorg/asio/issues/371#issuecomment-752293783  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#770](https://github.com/chriskohlhoff/asio/issues/770).
