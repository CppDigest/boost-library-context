# #298 - New container: double ended string? [Open]

> Username: denzor200  
> Created at: 2025-02-12 23:16:15 UTC  
> Updated at: 2025-02-12 23:16:15 UTC  
> Url: https://github.com/boostorg/container/issues/298  

By analogy with `boost::container::devector`, I think it would be useful to have a `boost::container::destring`.  
  
One of possible use cases is to create complicated concatenation chains without memory allocations(just my opinion, not sure does it worth, not sure does anybody really want it):  
  
```  
boost::container::destring str = "Hello world";  
str.reserve(str.size() + 2); // prepare to insert one element front and yet one back  
const auto braced_str = "(" + std::move(str) + ")"; // no memory allocation at all  
```
