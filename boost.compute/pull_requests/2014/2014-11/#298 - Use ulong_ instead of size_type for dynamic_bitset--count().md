# #298 Use ulong_ instead of size_type for dynamic_bitset::count() [Merged]

> Username: kylelutz  
> Created at: 2014-11-05 16:14:18 UTC  
> Updated at: 2014-11-06 04:03:05 UTC  
> Merged at: 2014-11-06 04:03:04 UTC  
> Closed at: 2014-11-06 04:03:04 UTC  
> Url: https://github.com/boostorg/compute/pull/298  

This fixes the following compilation error on Mac OS X:  
  
"error: implicit instantiation of undefined template  
'boost::compute::detail::type_name_trait<unsigned long>'"

---
