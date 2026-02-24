# #57 - Warnings in the Test Suite [Closed]

> Username: cmazakas  
> Created at: 2021-12-08 17:11:17 UTC  
> Updated at: 2022-02-14 20:48:10 UTC  
> Closed at: 2022-02-14 20:48:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/57  

This is a super issue for all compiler-generated warnings for the Unordered test suite.  
  
In `test/helpers/metafunctions.hpp` the following code generates `error: ‘this’ pointer is null [-Werror=nonnull]` with GCC 11:  
```cpp  
template <class Container> struct has_unique_keys  
{  
  static char flip(typename Container::iterator const&);  
  static long flip(std::pair<typename Container::iterator, bool> const&);  
  BOOST_STATIC_CONSTANT(bool,  
    value = sizeof(long) ==  
            sizeof(flip(  
              ((Container*)0)->insert(*(typename Container::value_type*)0))));  
};  
```  
  
Potential solutions include:  
```cpp  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wnonnull"  
    BOOST_STATIC_CONSTANT(bool,  
      value = sizeof(long) ==  
              sizeof(flip(  
                ((Container*)0)->insert(*(typename Container::value_type*)0))));  
#pragma GCC diagnostic pop  
```  
and:  
```cpp  
value = sizeof(long) == sizeof(flip(unordered_declval<Container&>().insert(unordered_declval<typename Container::value_type>())))  
```  
for when `unordered_declval` exists.

---

## Comment 1

> Username: cmazakas  
> Created at: 2022-02-14 20:48:09 UTC  
> Url: https://github.com/boostorg/unordered/issues/57#issuecomment-1039541956  

Closed by https://github.com/boostorg/unordered/commit/79ca8e968cd80ad1ec639374fde0be2d6b665e24
