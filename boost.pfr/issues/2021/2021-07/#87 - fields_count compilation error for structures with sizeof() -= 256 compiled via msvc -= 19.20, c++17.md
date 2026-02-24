# #87 - fields_count compilation error for structures with sizeof() >= 256 compiled via msvc <= 19.20, c++17 [Closed]

> Username: alexey-romanov  
> Created at: 2021-07-13 13:29:00 UTC  
> Updated at: 2021-07-26 06:15:39 UTC  
> Closed at: 2021-07-26 06:15:39 UTC  
> Url: https://github.com/boostorg/pfr/issues/87  

Trying to use `boost::pfr::for_each_field` on structures with sizeof() >= 256 on msvc compiler version <= 19.20 (e.g. all the versions of Visual Studio 2017) gives the following error:  
  
`boost/pfr/detail/fields_count.hpp(101): fatal error C1202: recursive type or function dependency context too complex`  
  
Structure field count doesn't matter just the size does.  
  
### Samples:  
  
1. Compilation error, structure size is exact 256 bytes  
Link to this sample on Godbolt: https://godbolt.org/z/3TdzddMM7  
```c++  
#include "boost/pfr.hpp"  
  
#pragma pack(push, 1)  
struct Foo {  
    long long a0;  
    long long a1;  
    long long a2;  
    long long a3;  
    long long a4;  
    long long a5;  
    long long a6;  
    long long a7;  
    long long a8;  
    long long a9;  
    long long a10;  
    long long a11;  
    long long a12;  
    long long a13;  
    long long a14;  
    long long a15;  
    long long a16;  
    long long a17;  
    long long a18;  
    long long a19;  
    long long a20;  
    long long a21;  
    long long a22;  
    long long a23;  
    long long a24;  
    long long a25;  
    long long a26;  
    long long a27;  
    long long a28;  
    long long a29;  
    long long a30;  
    long long a31;  
};  
#pragma pack(pop)  
  
void test()  
{  
    Foo foo;  
    boost::pfr::for_each_field(foo, [](auto& field) { });  
}  
```  
2. No compilation error, structure size is 255 bytes  
```c++  
#include "boost/pfr.hpp"  
  
#pragma pack(push, 1)  
struct Foo {  
    long long a0;  
    long long a1;  
    long long a2;  
    long long a3;  
    long long a4;  
    long long a5;  
    long long a6;  
    long long a7;  
    long long a8;  
    long long a9;  
    long long a10;  
    long long a11;  
    long long a12;  
    long long a13;  
    long long a14;  
    long long a15;  
    long long a16;  
    long long a17;  
    long long a18;  
    long long a19;  
    long long a20;  
    long long a21;  
    long long a22;  
    long long a23;  
    long long a24;  
    long long a25;  
    long long a26;  
    long long a27;  
    long long a28;  
    long long a29;  
    long long a30;  
    int i0;  
    char c0;  
    char c1;  
    char c2;  
};  
#pragma pack(pop)  
  
void test()  
{  
    Foo foo;  
    boost::pfr::for_each_field(foo, [](auto& field) { });  
}  
```  
3. Compilation error, less fields structure size is > 256 (280 to be precise)  
```c++  
#include "boost/pfr.hpp"  
#include <string>  
  
#pragma pack(push, 1)  
struct Foo {  
    std::string s0;  
    std::string s1;  
    std::string s2;  
    std::string s3;  
    std::string s4;  
    std::string s5;  
    std::string s6;  
    std::string s7;  
    std::string s8;  
    std::string s9;  
   };  
#pragma pack(pop)  
  
void test()  
{  
    Foo foo;  
    boost::pfr::for_each_field(foo, [](auto& field) { });  
}  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-07-13 14:40:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/87#issuecomment-879147777  

That's a compiler issue. Please, report it to the compiler vendors.  
  
Do you know any workarounds for the issue?

---

## Comment 2

> Username: alexey-romanov  
> Created at: 2021-07-14 08:59:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/87#issuecomment-879721421  

At the core the error comes from `enable_if_constructible_helper<T>(detail::make_index_sequence<N>())`. It appears that for VS2017 family compilers there is a limit of max 1024 element in a template parameter pack.  
Regarding the workaround, what about taking the min(sizeof(T)*BIT_COUNT, 1024) for VS2017? This would work for majority of the cases and give errors otherwise.  
Here is the pull request [https://github.com/boostorg/pfr/pull/88](https://github.com/boostorg/pfr/pull/88)
