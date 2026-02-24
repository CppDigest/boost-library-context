# #42 - boost::system::generic_category() return reference to static value but only inside compile unit [Closed]

> Username: ledocc  
> Created at: 2019-04-24 14:12:36 UTC  
> Updated at: 2019-04-25 18:07:05 UTC  
> Closed at: 2019-04-25 18:07:04 UTC  
> Url: https://github.com/boostorg/system/issues/42  

Since boost 1.70.0, when constexpr is supported by the compiler, boost::system::generic_category() return a reference to static instance of boost::system::detail::system_error_category, but in different compile unit, It return a reference to different instance.   
  
1.hpp  
```  
std::error_code get_error_code_1();  
```  
  
1.cpp  
```  
std::error_code get_error_code_1()  
{  
    boost::system::make_error_code( boost::system::file_exists );  
}  
```  
  
  
2.hpp  
```  
std::error_code get_error_code_2();  
```  
  
2.cpp  
```  
std::error_code get_error_code_2()  
{  
    boost::system::make_error_code( boost::system::file_exists );  
}  
```  
  
3.cpp  
```  
assert( get_error_code_1() == get_error_code_2() );  
```  
in 3.cpp, assertion fail because std::error_code::operator ==(...) compare category reference and there are not equal because they are created in different compile unit.  
  
  
  
To fix this, boost::system have to:  
  
- drop header only status ( hit since 1.69.0 ) and move static variable instantiation in .cpp file  
or  
- drop constexpr from boost::system::generic_category(), Is it really useful ? standard itself don't do this. But available in Boost 1.70.0, so remove it will potentially break user code  
or  
- drop std::error_code compatibility that will break user code

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-24 14:57:40 UTC  
> Url: https://github.com/boostorg/system/issues/42#issuecomment-486278050  

Thanks for the report. What compiler are you using?

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-24 16:11:22 UTC  
> Url: https://github.com/boostorg/system/issues/42#issuecomment-486309920  

I'm having trouble reproducing the problem. I added a test in https://github.com/boostorg/system/commit/9f225112f12264d3f9c85cb6d7c2806554c6088d that does what you outlined, but it passes on all compilers, whether or not `constexpr` is enabled.

---

## Comment 3

> Username: ledocc  
> Created at: 2019-04-24 17:42:40 UTC  
> Url: https://github.com/boostorg/system/issues/42#issuecomment-486354965  

g++ (Debian 6.3.0-18+deb9u1) 6.3.0 20170516  
  
I test with std_ec_mismatch_{1,2}.cpp in separate shared library and I reproduce the bug. d5dd74b

---

## Comment 4

> Username: pdimov  
> Created at: 2019-04-24 18:02:15 UTC  
> Url: https://github.com/boostorg/system/issues/42#issuecomment-486362065  

Separate shared libraries will fail, yes, but not because of the constexpr or because of generic_category's instance. They fail because the static map in to_std_category: https://github.com/boostorg/system/blob/48b8a6c41cfe804138463c356409ae5038970282/include/boost/system/detail/std_interoperability.hpp#L60 has two separate instances in the two shared libraries.  
  
This can't be fixed without dropping header-only and moving the std_interoperability.hpp code into a shared Boost.System library.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-04-24 18:03:46 UTC  
> Url: https://github.com/boostorg/system/issues/42#issuecomment-486362606  

Or maybe it can, on Linux, by marking the function VISIBLE. Not on Windows though.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-04-25 18:07:04 UTC  
> Url: https://github.com/boostorg/system/issues/42#issuecomment-486779260  

Should be fixed in develop.
