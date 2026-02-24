# #41 - endian_reverse template should only participate in overload resolution if T is integral [Closed]

> Username: u3shit  
> Created at: 2020-01-12 19:41:07 UTC  
> Updated at: 2020-01-13 16:18:12 UTC  
> Closed at: 2020-01-13 16:18:12 UTC  
> Url: https://github.com/boostorg/endian/issues/41  

I had a code like this to automatically call `endian_reverse_inplace` for my custom classes:  
```c++  
  template <typename T, typename = std::enable_if_t<!std::is_integral_v<T>>>  
  static T endian_reverse(T t) noexcept  
  {  
    endian_reverse_inplace(t);  
    return t;  
  }  
```  
(I know, that enable_if might be a bit too broad, but I only call endian_reverse on integral types and my own structs of them, so it's probably okay).  
As far as I can see from the documentation, EndianReversible requires that `endian_reverse(x)` is well-formed, it doesn't mention anywhere it can't be a template. With boost 1.70.0 this worked fine, but after updating to 1.72.0 compilation fails with `call to 'endian_reverse' is ambiguous`. If I remove my `endian_reverse` function I get a static assert instead from here: https://github.com/boostorg/endian/blob/3d053160ce3162861fcab6a3ae454f9a462bfe47/include/boost/endian/detail/endian_reverse.hpp#L111

---

## Comment 1

> Username: pdimov  
> Created at: 2020-01-13 14:25:32 UTC  
> Url: https://github.com/boostorg/endian/issues/41#issuecomment-573687786  

Yes, you're right, this looks like a legitimate regression.  
  
Your endian_reverse seems to cause infinite recursion when called on anything not defining endian_reverse_inplace, by the way.
