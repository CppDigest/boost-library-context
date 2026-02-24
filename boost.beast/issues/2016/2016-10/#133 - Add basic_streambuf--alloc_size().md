# #133 - Add basic_streambuf::alloc_size() [Closed]

> Username: vinniefalco  
> Created at: 2016-10-14 14:57:50 UTC  
> Updated at: 2016-10-20 22:00:10 UTC  
> Closed at: 2016-10-20 22:00:10 UTC  
> Url: https://github.com/boostorg/beast/issues/133  

Synopsis  
  
```  
  /// Set the allocation block size  
  void alloc_size(std::size_t bytes);  
  
  /// Returns the default size of allocated blocks  
  std::size_t alloc_size() const;  
```
