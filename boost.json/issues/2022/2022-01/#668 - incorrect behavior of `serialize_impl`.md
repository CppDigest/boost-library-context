# #668 - incorrect behavior of `serialize_impl` [Closed]

> Username: vinniefalco  
> Created at: 2022-01-12 03:30:51 UTC  
> Updated at: 2022-01-16 17:20:48 UTC  
> Closed at: 2022-01-16 17:20:48 UTC  
> Url: https://github.com/boostorg/json/issues/668  

`serialize_impl` is used to serialize JSON into a `std::string`:  
  
https://github.com/boostorg/json/blob/307d190bcfc0707fc532839b3be78e6e3b800519/include/boost/json/impl/serialize.ipp#L21  
  
The problem is that line 52 is incorrect, I think it should read:  
```  
s.reserve( s.capacity() + 1 );  
s.resize( s.capacity() );  
```  
  
otherwise, every 2nd trip through the `for` loop will be a 1-char write.
