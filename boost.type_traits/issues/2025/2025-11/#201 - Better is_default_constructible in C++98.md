# #201 - Better is_default_constructible in C++98 [Open]

> Username: Kim-J-Smith  
> Created at: 2025-11-17 14:06:34 UTC  
> Updated at: 2025-11-17 14:06:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/201  

- Lines 84 to 92 in boost/type_traits/is_default_constructible.hpp are the compromise solution without std::decltype. In fact, there are better solutions.  
  
---  
  
* The original code is as follows(line 84 ~ 92 in boost/type_traits/is_default_constructible.hpp)  
  
```c++  
   // We don't know how to implement this, note we can not use has_trivial_constructor here  
   // because the correct implementation of that trait requires this one:  
   template <class T> struct is_default_constructible : public is_pod<T>{};  
   template <> struct is_default_constructible<void> : public integral_constant<bool, false>{};  
   template <> struct is_default_constructible<void const> : public integral_constant<bool, false>{};  
   template <> struct is_default_constructible<void volatile> : public integral_constant<bool, false>{};  
   template <> struct is_default_constructible<void const volatile> : public integral_constant<bool, false>{};  
```  
  
* Better solution (without std::decltype or other C++11 feature)  
  
```c++  
  // __is_default_constructible_helper  
  template <typename T, bool not_void>  
  struct __is_default_constructible_helper  
  {  
    typedef char        yes[1];  
    typedef char        no[2];  
  
    template <typename U>  
    static yes& check(int (*) [sizeof((U()))]);  
  
    template <typename U>  
    static no& check(...);  
  
    static const bool value = sizeof(check<T>(0)) == sizeof(yes);  
  };  
  
  // void : false  
  template <typename T>  
  struct __is_default_constructible_helper<T, false>  
  : public false_type { };  
  
  // T[S] -> T  
  template <typename T, size_t S>  
  struct __is_default_constructible_helper<T[S], true>  
  : public __is_default_constructible_helper<T, true> { };  
  
  // T[] : false  
  template <typename T>  
  struct __is_default_constructible_helper<T[], true>  
  : public false_type { };  
  
  // T&& and T& : false  
  template <typename T>  
  struct __is_default_constructible_helper<T&, true>  
  : public false_type { };  
  
#if __cplusplus >= 201103L  
  template <typename T>  
  struct __is_default_constructible_helper<T&&, true>  
  : public false_type { };  
#endif // __cplusplus >= 201103L  
  
  /// is_default_constructible  
  template <typename T>  
  struct is_default_constructible  
  : public kim::bool_constant<  
    __is_default_constructible_helper<  
      typename kim::__remove_cv<T>::type, !is_void<T>::value  
    >::value  
  >::type  
  { };  
```
