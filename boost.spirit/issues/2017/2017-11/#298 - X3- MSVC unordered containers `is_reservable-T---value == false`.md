# #298 - X3: MSVC unordered containers `is_reservable<T>::value == false` [Closed]

> Username: octopus-prime  
> Created at: 2017-11-27 20:55:06 UTC  
> Updated at: 2017-12-16 19:10:01 UTC  
> Closed at: 2017-12-16 19:10:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/298  

Until we have a good integration for boostorg/spirit in appveyor - this is what we get for now (via private appveyor account):  
  
```  
compile-c-c++ bin.v2\libs\spirit\test\x3\x3_container_support-p3.test\msvc-14.1\release\address-model-64\threadapi-win32\threading-multi\container_support.obj  
container_support.cpp  
libs\spirit\test\x3\container_support.cpp(181): error C2338: is_reservable problem  
libs\spirit\test\x3\container_support.cpp(183): error C2338: is_reservable problem  
libs\spirit\test\x3\container_support.cpp(185): error C2338: is_reservable problem  
libs\spirit\test\x3\container_support.cpp(187): error C2338: is_reservable problem  
```  
  
https://ci.appveyor.com/project/octopus-prime/spirit

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-11-27 21:05:57 UTC  
> Updated at: 2017-11-27 21:13:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347327802  

The errors came from reservable-tests of std::unordered_xyz classes.  
  
So this is the standard  
http://en.cppreference.com/w/cpp/container/unordered_map  
with reserve()  
  
And this is boost  
http://www.boost.org/doc/libs/1_65_1/doc/html/boost/unordered_map.html  
with reserve()  
  
But (of course) this is microsoft  
https://msdn.microsoft.com/de-de/library/bb982522.aspx  
WITHOUT reserve() :-1:

---

## Comment 2

> Username: Kojoley  
> Created at: 2017-11-28 13:19:38 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347521020  

This perfectly compiles on VS 2015, 2017:  
  
```cpp  
#include <unordered_map>  
#include <unordered_set>  
  
int main()  
{  
    std::unordered_map<int, int> umap;  
    umap.reserve(1);  
  
    std::unordered_set<int> uset;  
    uset.reserve(1);  
  
    std::unordered_multimap<int, int> ummap;  
    ummap.reserve(1);  
  
    std::unordered_multiset<int> umset;  
    umset.reserve(1);  
  
    return 0;  
}  
```  
  
It should be a problem in traits.

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-11-28 13:34:31 UTC  
> Updated at: 2017-11-28 13:48:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347524728  

Would like to find the difference.  
I don't have MSVC - so can not try to find it.  
Can only read documentation to find the difference in reserve method.  
But documentation says: no reserve method.  
  
https://msdn.microsoft.com/en-us/library/bb982522.aspx

---

## Comment 4

> Username: Kojoley  
> Created at: 2017-11-28 22:31:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347686177  

Here is the problem:  
  
MSVC unordered containers have a base [`_Hash`](http://sandbox.hlt.bme.hu/~gaebor/STLdoc/VS2017/class__Hash.html) from which [`reserve`](http://sandbox.hlt.bme.hu/~gaebor/STLdoc/VS2017/class__Hash.html#a707a5b3d8d095772c3bf47094b740067) member comes from:  
  
```cpp  
	class unordered_map  
		: public _Hash<_Umap_traits<_Kty, _Ty,  
			_Uhash_compare<_Kty, _Hasher, _Keyeq>, _Alloc, false>>  
```  
  
but `BOOST_TTI_HAS_MEMBER_FUNCTION` cannot introspect for inherited members https://groups.google.com/forum/#!topic/boost-list/awd4kqJezfQ  
  
```cpp  
#include <boost/tti/has_member_function.hpp>  
#include <iostream>  
  
BOOST_TTI_HAS_MEMBER_FUNCTION(function1);  
  
struct A { int function1(); };  
struct B : A {};  
  
int main()  
{  
    std::cout << std::boolalpha  
        << has_member_function_function1<A, int>::value << std::endl // 1. true  
        << has_member_function_function1<B, int>::value << std::endl // 2. false  
        ;  
    return 0;  
}  
```

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-11-28 22:36:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347687381  

Excellent analysis!

---

## Comment 6

> Username: octopus-prime  
> Created at: 2017-11-29 14:25:43 UTC  
> Updated at: 2017-11-29 14:26:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347875489  

Possible solution:  
  
```cpp  
#include <iostream>  
  
struct A {  
  void reserve(size_t);  
};  
struct B : A {};  
  
// For this approach we need a void_t  
// We could use std::void_t (but it's c++17)  
// Is there something equivalent in boost?!  
// If not we can define it by:  
template <typename... T> using void_t = void;  
  
template <typename T, typename = void>  
struct has_reserve : std::false_type {};  
  
template <typename T>  
struct has_reserve<T, void_t<  
    decltype(std::declval<T&>().reserve(size_t{}))  
>> : std::true_type {};  
  
template <typename T, typename = void>  
struct has_foo : std::false_type {};  
  
template <typename T>  
struct has_foo<T, void_t<  
    decltype(std::declval<T&>().foo(size_t{}))  
>> : std::true_type {};  
  
int main() {  
  std::cout << std::boolalpha  
            << has_reserve<A>::value << std::endl // true  
            << has_reserve<B>::value << std::endl // true  
            << has_foo<A>::value << std::endl     // false  
            << has_foo<B>::value << std::endl     // false  
      ;  
  return 0;  
}  
```  
  
Can somebody check whether this code works with MSVC?

---

## Comment 7

> Username: MarcelRaad  
> Created at: 2017-11-29 14:29:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347876637  

@octopus-prime Works fine with MSVC 14.11 (2017 Update 3+).

---

## Comment 8

> Username: Kojoley  
> Created at: 2017-11-29 14:29:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347876674  

It should, because it looks like that one I has  
https://github.com/boostorg/spirit/commit/45902cbb4e949e7543bcf48d3a4ed1247cab4f6b

---

## Comment 9

> Username: octopus-prime  
> Created at: 2017-11-29 14:36:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/298#issuecomment-347878918  

@Kojoley Really nice job!! Without void_t at all...
