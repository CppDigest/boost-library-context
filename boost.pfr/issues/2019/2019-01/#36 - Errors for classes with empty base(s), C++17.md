# #36 - Errors for classes with empty base(s), C++17 [Open]

> Username: zmij  
> Created at: 2019-01-18 17:52:51 UTC  
> Updated at: 2024-10-17 09:15:00 UTC  
> Url: https://github.com/boostorg/pfr/issues/36  

Provided the following hierarchy of classes:  
  
```C++  
namespace empty_base {  
  
struct A {};  
struct B {};  
  
// Empty base, no members  
struct C : A {};  
// Empty base, some members  
struct D : A { double d; };  
// Two empty bases, no members  
struct E : A, B {};  
// Two empty bases, some membes  
struct F : A, B { float f; };  
  
}  
```  
  
When trying to use pfr with them there are the following errors (the error messages are somewhat shortened):  
  
**_Class with an empty base and no members_**  
_Precise:_  
```C++  
> decltype(structure_to_tuple(declval<empty_base::C>()))  
./include/boost/pfr/detail/core17_generated.hpp:39:9: fatal error: type 'const empty_base::C' decomposes into 0 elements, but 1 names were provided  
  auto& [a] = val;  
        ^  
./include/boost/pfr/detail/core17_generated.hpp:1037:30: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_tuple<const empty_base::C>' requested here  
  return boost::pfr::detail::tie_as_tuple(val, fields_count_tag{});  
                             ^  
./include/boost/pfr/precise/core.hpp:93:17: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_tuple<const empty_base::C>' requested here  
        detail::tie_as_tuple(val),  
```  
_Flat:_  
```C++  
> decltype(flat_structure_to_tuple(declval<empty_base::C>()))  
std::tuple<> // This is fine, actually  
```  
  
**_Class with an empty base and some members_**  
_Presize:_  
```C++  
> decltype(structure_to_tuple(declval<empty_base::D>()))  
./include/boost/pfr/detail/core17_generated.hpp:52:9: fatal error: type 'const empty_base::D' decomposes into 1 elements, but 2 names were provided  
  auto& [a,b] = val;  
        ^  
./include/boost/pfr/detail/core17_generated.hpp:1037:30: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_tuple<const empty_base::D>' requested here  
  return boost::pfr::detail::tie_as_tuple(val, fields_count_tag{});  
                             ^  
./include/boost/pfr/precise/core.hpp:93:17: note: in instantiation of function template specialization 'boost::pfr::detail::tie_as_tuple<const empty_base::D>' requested here  
        detail::tie_as_tuple(val)  
```  
_Flat:_  
```C++  
> decltype(flat_structure_to_tuple(declval<empty_base::D>()))  
./include/boost/pfr/detail/offset_based_getter.hpp:63:3: fatal error: static_assert failed "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!"  
  static_assert(sizeof(U) == sizeof(S), "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!");  
// Nice assert, but doesn't help, actually  
```  
  
**_Class with two empty bases and no members_**  
_Precise:_  
```C++  
> decltype(structure_to_tuple(declval<empty_base::E>()))  
./include/boost/pfr/detail/core17_generated.hpp:52:9: fatal error: type 'const empty_base::E' decomposes into 0 elements, but 2 names were provided  
  auto& [a,b] = val;  
```  
_Flat:_  
```C++  
> decltype(flat_structure_to_tuple(declval<empty_base::E>()))  
./include/boost/pfr/detail/offset_based_getter.hpp:63:3: fatal error: static_assert failed "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!"  
  static_assert(sizeof(U) == sizeof(S), "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!");  
```  
  
**_Class with two empty bases and some members_**  
_Precise:_  
```C++  
> decltype(structure_to_tuple(declval<empty_base::F>()))  
./include/boost/pfr/detail/core17_generated.hpp:58:9: fatal error: type 'const empty_base::F' decomposes into 1 elements, but 3 names were provided  
  auto& [a,b,c] = val;  
```  
_Flat:_  
```C++  
> decltype(flat_structure_to_tuple(declval<empty_base::F>()))  
./include/boost/pfr/detail/offset_based_getter.hpp:63:3: fatal error: static_assert failed "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!"  
  static_assert(sizeof(U) == sizeof(S), "====================> Boost.PFR: Member sequence does not indicate correct size for struct type!");  
```

---

## Comment 1

> Username: hadrielk  
> Created at: 2020-09-28 12:01:58 UTC  
> Url: https://github.com/boostorg/pfr/issues/36#issuecomment-699963418  

I got the same issue with a class that derives from a CRTP-based empty base class. (`boost::orable` in my case, from [boost operators](https://www.boost.org/doc/libs/1_74_0/libs/utility/operators.htm))  
  
I'm just noting it so that CRTP-bases are added as a test, if/when this issue is fixed. (CRTP empty bases are fairly common, I think)

---

## Comment 2

> Username: apolukhin  
> Created at: 2021-04-09 07:12:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/36#issuecomment-816467114  

The diagnostic was added in https://github.com/boostorg/pfr/commit/180db174ad5543577e525ca230974a98fac13004#diff-9f25dfaeef0127087c5ed7f25def6deb967c8b1cef9427801f07782652a0044d and https://github.com/boostorg/pfr/commit/ea4c6e85f734b0ed9f4233ba9654b877398fa948#diff-9f25dfaeef0127087c5ed7f25def6deb967c8b1cef9427801f07782652a0044d  
  
Will merge to master after 1.76 release
