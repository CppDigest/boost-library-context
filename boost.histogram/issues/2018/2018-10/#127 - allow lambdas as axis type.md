# #127 - allow lambdas as axis type [Closed]

> Username: HDembinski  
> Created at: 2018-10-26 07:13:00 UTC  
> Updated at: 2018-11-29 17:20:15 UTC  
> Closed at: 2018-11-29 17:19:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/127  

It should be possible to use lambdas as axis objects. The lambda should return `std::pair<int, unsigned>`, an index and the extend of the axis.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-11-29 17:19:52 UTC  
> Updated at: 2018-11-29 17:20:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/127#issuecomment-442918646  

After careful consideration, I will not implement this. A minimal axis can be defined locally already as a non-templated struct. In a local context, all types for the axis are known, so there is no need for templated structs, which cannot be defined locally. A struct therefore can do whatever a lambda could do.  
  
Lambdas would allow an even briefer definition of a simple axis, but defining a local axis for one-time use is a pretty artificial use-case anyway. A struct has the advantage that you can easily extend it later. You are required to only provide a minimal implementation  
```  
struct minimal_axis {  
  int operator()(double x) const { return 0; }  
  unsigned size() const {}  
};  
```  
But once you have that, you can extend it further by providing iterators, a `value(...)` method and so on. You cannot easily extend a lambda in the same way.
