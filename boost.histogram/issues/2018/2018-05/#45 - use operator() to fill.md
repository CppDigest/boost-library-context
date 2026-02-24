# #45 - use operator() to fill [Closed]

> Username: HDembinski  
> Created at: 2018-05-02 08:04:25 UTC  
> Updated at: 2018-07-04 23:19:47 UTC  
> Closed at: 2018-07-04 23:19:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/45  

This allows compact STL code like this  
```  
auto h = make_static_histogram(...);  
std::foreach(container.begin(), container.end(), h);  
```  
and it is compatible with the Boost Accumulator interface, which is important for consistency.
