# #918 - BOOST_UNLIKELY and boost::system::error_code [Open]

> Username: ujos  
> Created at: 2024-06-04 10:37:12 UTC  
> Updated at: 2024-06-19 16:06:01 UTC  
> Url: https://github.com/boostorg/boost/issues/918  

Could you please modify the `BOOST_LIKELY` and `BOOST_UNLIKELY` macros in the following way:  
  
```cpp  
#define BOOST_LIKELY(x)   (__builtin_expect(!!(x), 1))  
#define BOOST_UNLIKELY(x) (__builtin_expect(!!(x), 0))  
  
```  
  
1. I cannot easily use `BOOST_UNLIKELY` with `boost::system::error_code`. Compiler complains   
  
```  
error: cannot convert ‘boost::system::error_code’ to ‘long int’ for argument ‘1’ to ‘long int __builtin_expect(long int, long int)’  
[build]      if (BOOST_UNLIKELY (ec)) {  
[build]          ^   
```  
  
To workaround the problem I should use macro like following:  
```  
if (BOOST_UNLIKELY (!!ec))  
```  
... which looks weird and causes unwanted questions during the code review.  
  
2. I have to add extra braces:  
  
```cpp  
if (BOOST_UNLIKELY (!!ec))  
   ^ -- here             ^ --- and here  
```  
... instead of:  
  
  
```cpp  
if BOOST_UNLIKELY (ec)  
{  
}  
```

---

## Comment 1

> Username: Vasco0x4  
> Created at: 2024-06-19 16:05:42 UTC  
> Updated at: 2024-06-19 16:06:01 UTC  
> Url: https://github.com/boostorg/boost/issues/918#issuecomment-2179049219  

To modify the BOOST_LIKELY and BOOST_UNLIKELY macros to handle ```boost::system::error_code```, you can redefine them to ensure compatibility without needing additional braces. Here's the updated version:  
  
```#define BOOST_LIKELY(x)   (__builtin_expect(static_cast<bool>(x), 1))  
#define BOOST_UNLIKELY(x) (__builtin_expect(static_cast<bool>(x), 0))  
```  
  
This approach uses ```static_cast<bool>(x)``` to explicitly cast the condition to a boolean type, making it compatible with ```boost::system::error_code``` and similar types. This allows you to use the macros as follows:  
  
```if (BOOST_UNLIKELY(ec)) {  
    // Handle error  
}  
```  
This change ensures the macros work correctly without needing extra braces.
