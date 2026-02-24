# #151 - Addressing the limitation of `get_name` (non-internal linkage) [Closed]

> Username: schaumb  
> Created at: 2023-11-29 12:21:58 UTC  
> Updated at: 2024-01-28 08:52:59 UTC  
> Closed at: 2024-01-28 08:52:59 UTC  
> Url: https://github.com/boostorg/pfr/issues/151  

Currently, there is a [limitation](https://www.boost.org/doc/libs/develop/doc/html/boost_pfr/limitations_and_configuration.html) on `pfr::get_name`:  
  
> The Boost.PFRs extraction of field name has some limitations that depend on a C++ Standard and compiler capabilities:  
> - T should be usable like extern T t;, i.e. has a non-internal linkage.  
  
([related issue](https://github.com/boostorg/pfr/issues/144))  
  
This can be bypassed with the following hack:  
https://godbolt.org/z/ajEz33x4z  
  
  
Tldr, replace [this](https://github.com/boostorg/pfr/blob/98789f610a8bab53d5523157ff6a3fac7606764a/include/boost/pfr/detail/fake_object.hpp#L19C1-L31C2):  
```cpp  
template <class T>  
extern const T passed_type_has_no_external_linkage;  
  
template <class T>  
constexpr const T& fake_object() noexcept {  
    return passed_type_has_no_external_linkage<T>;  
}  
```  
  
To this:  
  
  
```cpp  
template<class T>  
union union_wrapper {  
    const T value;  
};   
  
template <class T>  
extern const union_wrapper<T> passed_type;  
  
template <class T>  
constexpr const T& fake_object() noexcept {  
    return passed_type<T>.value;  
}  
```  
  
---   
  
Why does it work? - I don't know but it's work.

---

## Comment 1

> Username: schaumb  
> Created at: 2023-11-29 12:31:39 UTC  
> Updated at: 2023-11-29 12:52:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/151#issuecomment-1831808113  

It works with a struct wrapper too.   
```cpp  
template<class T>  
struct struct_wrapper {  
    const T value;  
};   
  
template <class T>  
extern const struct_wrapper<T> passed_type;  
  
template <class T>  
constexpr const T& fake_object() noexcept {  
    return passed_type<T>.value;  
}  
```  
  
---  
  
And it works because `struct_wrapper` (and `union_wrapper`) has an external linkage

---

## Comment 2

> Username: denzor200  
> Created at: 2023-11-29 21:39:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/151#issuecomment-1832741423  

Nice catch, @schaumb !  
But it might a bug, not a feature. Looks like it will be fixed in future compilers
