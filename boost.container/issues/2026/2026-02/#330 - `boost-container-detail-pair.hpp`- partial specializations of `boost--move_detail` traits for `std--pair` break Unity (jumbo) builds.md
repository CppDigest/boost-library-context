# #330 - `boost/container/detail/pair.hpp`: partial specializations of `boost::move_detail` traits for `std::pair` break Unity (jumbo) builds [Closed]

> Username: janzizazizka  
> Created at: 2026-02-09 09:54:56 UTC  
> Updated at: 2026-02-16 21:53:40 UTC  
> Closed at: 2026-02-16 21:53:40 UTC  
> Url: https://github.com/boostorg/container/issues/330  

## Summary                                                                                                 
                                                                                                           
`boost/container/detail/pair.hpp` defines partial specializations of                                       
`boost::move_detail::is_trivially_copy_constructible` (and four other traits)  
for `std::pair<A,B>`.  The primary templates for these traits live in a  
different header: `boost/move/detail/type_traits.hpp`.                                                     
                                                      
When a translation unit implicitly instantiates one of these traits for a                                  
`std::pair<…>` type **before** `boost/container/detail/pair.hpp` is included,                              
GCC diagnoses the later partial specialization as ill-formed:                                              
                                                                                                           
```                                                   
boost/container/detail/pair.hpp:606:8: error: partial specialization of                                    
  'struct boost::move_detail::is_trivially_copy_constructible<std::pair<_T1, _T2> >'                       
  after instantiation of                                                                                   
  'struct boost::move_detail::is_trivially_copy_constructible<std::pair<unsigned int, long unsigned int> >'  
  [-fpermissive]                                                                                           
```                                                                                                        
                                                                                                           
This is a hard error (not a warning) in GCC 15 and occurs regardless of the                                
values produced by the primary template and the specialization.                                                                                                                                                      
                                                                                                                                                                                                                     
The problem is triggered in **Unity (jumbo) builds** where multiple `.cpp`  
files are compiled as a single translation unit — the include order across  
files becomes significant.  It can also occur in a regular build if a single  
source file happens to include `boost/move/detail/type_traits.hpp` (or any  
header that pulls it in) and uses one of the traits for `std::pair<…>` before  
including any `boost/container` header.  
  
This issue is created with help of Cursor, I did go through all also manually but could be I have overlooked  
something.  
  
## Affected version  
  
Boost 1.90.0.    
  
**Boost 1.87.0 is NOT affected** (confirmed: builds  
successfully with GCC 15.2.1 and the same reproducer).  
  
This can be for example reproduced in Fedora 44.  
  
### Introduced in  
  
The `std::pair` specializations were added in commit   
[`9552828`](https://github.com/boostorg/container/commit/9552828c54a6729510d9d8935616055996586644)  
("Apply optimization traits also to std::pair", Sep 7 2025, by @igaztanaga),  
between Boost 1.87.0 and 1.90.0.  A follow-up commit   
[`814efb5`](https://github.com/boostorg/container/commit/814efb53d504ad1e3ac7ce140c140fa3270c6d04)  
("Remove redundant value in is_trivially_copy_assignable for std::pair")  
cleaned up the code but did not address the ordering issue.  
  
### Historical precedent  
  
[Trac #12534](https://svn.boost.org/trac/boost/ticket/12534) ("flat_map fails  
to compile if included after type_traits is instantiated under gcc", 2016) was  
the **exact same class of bug** — partial specializations of  
`boost::move_detail::is_enum` and `is_class` for `std::pair` in `pair.hpp`  
were seen after implicit instantiation.  It was fixed in commit  
[`5e4a107`](https://github.com/boostorg/container/commit/5e4a107e82ab3281688311d22d2bfc2fddcf84a3)  
by removing those specializations from `pair.hpp`.  Commit `9552828`  
reintroduced the same pattern for a different set of traits.  
  
## Affected traits  
  
All five `std::pair<A,B>` partial specializations in   
`boost/container/detail/pair.hpp` (lines 550–643) are affected:  
  
- `is_trivially_copy_assignable` (line 567)  
- `is_trivially_move_assignable` (line 586)  
- `is_trivially_copy_constructible` (line 606)  
- `is_trivially_move_constructible` (line 625)  
- `is_trivially_destructible` (line 640)  
  
The corresponding `boost::container::dtl::pair<A,B>` specializations in the  
same block are NOT affected because `boost::container::dtl::pair` is defined  
in the same header, so it cannot be instantiated before the specialization is  
seen.  
  
## Minimal reproducer  
  
Three files — uses Boost 1.90.0 headers directly, no custom types:  
  
**a.cpp** — implicitly instantiates the primary template:  
  
```cpp  
#include <utility>  
#include <boost/move/detail/type_traits.hpp>  
  
using P = std::pair<unsigned int, unsigned long>;  
  
// Implicit instantiation — accessing ::value forces the compiler to  
// instantiate the primary template.  
static constexpr bool trait_value =  
    boost::move_detail::is_trivially_copy_constructible<P>::value;  
```  
  
**b.cpp** — includes a header that brings in the partial specialization:  
  
```cpp  
#include <boost/container/flat_map.hpp>  
  
int main() {  
    boost::container::flat_map<int, int> m;  
    m.emplace(1, 2);  
    return m.size() == 1 ? 0 : 1;  
}  
```  
  
**CMakeLists.txt**:  
  
```cmake  
cmake_minimum_required(VERSION 3.16)  
project(boost_pair_repro CXX)  
set(CMAKE_CXX_STANDARD 17)  
  
add_executable(repro a.cpp b.cpp)  
set_target_properties(repro PROPERTIES UNITY_BUILD ON)  
```  
  
Build (requires GCC 15+ and Boost 1.90.0 headers):  
  
```bash  
cmake -B build .  
cmake --build build  
```  
  
Output (GCC 15.2.1, Boost 1.90.0):  
  
```  
boost/container/detail/pair.hpp:606:8: error: partial specialization of  
  'struct boost::move_detail::is_trivially_copy_constructible<std::pair<_T1, _T2> >'  
  after instantiation of  
  'struct boost::move_detail::is_trivially_copy_constructible<std::pair<unsigned int, long unsigned int> >'  
  [-fpermissive]  
  606 | struct is_trivially_copy_constructible<std::pair<A,B> >  
      |        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
This can be for example reproduced in Fedora 44.  
  
**Single-file variant** (no CMake / unity build required):  
  
```cpp  
// single_tu.cpp  
#include <utility>  
#include <boost/move/detail/type_traits.hpp>  
  
using P = std::pair<unsigned int, unsigned long>;  
static constexpr bool v =  
    boost::move_detail::is_trivially_copy_constructible<P>::value;  
  
#include <boost/container/flat_map.hpp>  
  
int main() {  
    boost::container::flat_map<int, int> m;  
    m.emplace(1, 2);  
    return m.size() == 1 ? 0 : 1;  
}  
```  
  
```bash  
g++ -std=c++17 single_tu.cpp -o repro   # fails with the same error  
```  
  
## Why this happens  
  
The C++ standard ([temp.spec.partial.general]) says that if a partial  
specialization of a class template is declared after the first implicit  
instantiation of that template for a type that would match the specialization,  
the program is ill-formed.  GCC 15 actively diagnoses this as a hard error.  
  
The key detail: GCC only diagnoses this for **implicit** instantiation (where  
a member like `::value` is accessed).  Explicit instantiation declarations  
(`template struct …;`) do not trigger the diagnostic.  In real-world code,  
Boost traits are always used implicitly.  
  
## Suggested fix  
  
Move the `std::pair<A,B>` partial specializations from  
`boost/container/detail/pair.hpp` into `boost/move/detail/type_traits.hpp`,  
next to the primary templates.  The `std::pair` specializations only depend on  
`<utility>` (for `std::pair`) and on the primary templates themselves — they  
do NOT depend on `boost::container::dtl::pair`.  
  
The `std::pair` specializations currently inherit from the  
`boost::container::dtl::pair` specializations, but this indirection can be  
replaced with direct member-wise checks (which is what the `dtl::pair`  
specialization does anyway):  
  
```cpp  
// In boost/move/detail/type_traits.hpp, after each primary template:  
  
template<class T>  
struct is_trivially_copy_constructible  
{  static const bool value = BOOST_MOVE_IS_TRIVIALLY_COPY_CONSTRUCTIBLE(T); };  
  
// Add std::pair specialization here:  
template<class A, class B>  
struct is_trivially_copy_constructible<std::pair<A,B> >  
{  
   static const bool value = is_trivially_copy_constructible<A>::value &&  
                             is_trivially_copy_constructible<B>::value;  
};  
  
// ... same pattern for is_trivially_move_constructible,  
//     is_trivially_copy_assignable, is_trivially_move_assignable,  
//     is_trivially_destructible  
```  
  
The `boost::container::dtl::pair` specializations can remain in  
`boost/container/detail/pair.hpp` since `dtl::pair` is defined there.  
  
## Current user-level workaround  
  
Force-include the specialization header early in every TU:  
  
```cmake  
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID STREQUAL "Clang")  
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -include boost/container/detail/pair.hpp")  
endif()  
```  
  
## Environment  
  
- GCC 15.2.1 (x86_64, `-std=c++17` and `-std=c++23`)   
- Boost 1.90.0 (reproduces), Boost 1.87.0 (does NOT reproduce)  
- CMake 3.31 with `UNITY_BUILD ON`  
- Also confirmed in production CI builds (both x86 and ARM cross-compilation  
  with `--sysroot`) using Unity builds with hundreds of translation units  
- Fedora 44

---

## Comment 1

> Username: janzizazizka  
> Created at: 2026-02-09 09:59:34 UTC  
> Url: https://github.com/boostorg/container/issues/330#issuecomment-3870680076  

I did try to make fix for this issue in https://github.com/boostorg/container/pull/331 and https://github.com/boostorg/move/pull/64 but I'm not sure if this could have some side effects and if this is right way to handle this.

---

## Comment 2

> Username: janzizazizka  
> Created at: 2026-02-09 10:01:17 UTC  
> Url: https://github.com/boostorg/container/issues/330#issuecomment-3870688948  

If that helps here are files with the reproducer.  
  
[test_all.sh](https://github.com/user-attachments/files/25180037/test_all.sh)  
[CMakeLists.txt](https://github.com/user-attachments/files/25180038/CMakeLists.txt)  
[single_tu.cpp](https://github.com/user-attachments/files/25180036/single_tu.cpp)  
[b.cpp](https://github.com/user-attachments/files/25180035/b.cpp)  
[a.cpp](https://github.com/user-attachments/files/25180034/a.cpp)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2026-02-16 21:52:35 UTC  
> Url: https://github.com/boostorg/container/issues/330#issuecomment-3910655839  

Thanks for the detailed report. After applying https://github.com/boostorg/move/pull/64, I'm removing the specializations from Boost.Container, as your report proposes. I've also transferred to Boost.Move other specialization like "is_class_or_union", "is_class", "is_union" or "has_trivial_destructor_after_move"
