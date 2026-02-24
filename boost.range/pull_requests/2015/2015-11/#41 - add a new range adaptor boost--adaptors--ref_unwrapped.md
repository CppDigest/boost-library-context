# #41 add a new range adaptor boost::adaptors::ref_unwrapped [Merged]

> Username: robin-zimmeck  
> Created at: 2015-11-05 09:26:41 UTC  
> Updated at: 2016-01-04 16:38:36 UTC  
> Merged at: 2016-01-04 16:38:36 UTC  
> Closed at: 2016-01-04 16:38:36 UTC  
> Url: https://github.com/boostorg/range/pull/41  

This new adaptor is to simplify range-for iteration of ranges with `std::reference_wrapper` values.  
It is similar to `boost::adaptors::indirected`, but instead of dereferencing using `operator*`, it calls `.get()` on the range values.  
  
As `std::reference_wrapper` is C++11 (and above) only, this adaptor is C++11 (and above) only, too.  
  
If, for example you have a struct or class:  
  
```  
struct example  
{  
    int value;  
};  
```  
  
and a range using std::reference_wrapper:  
  
```  
std::vector<std::reference_wrapper<example>> items;  
```  
  
and are using range-for and auto:  
  
```  
for (auto&& item : items)  
{  
    std::cout << item.value; // won't compile, as .get() is missing and not automatically converted  
    std::cout << item.get().value;  
}  
```  
  
this can be simplified using the new adaptor  
  
```  
for (auto&& item : items | boost::adaptors::ref_unwrapped)  
{  
    std::cout << item.value;  
}  
```  
  
Of course, this works in other context where the implicit conversion is not available, too.  
  
I tested it using gcc 4.8.4, clang 3.5 and msvc 12.

---
