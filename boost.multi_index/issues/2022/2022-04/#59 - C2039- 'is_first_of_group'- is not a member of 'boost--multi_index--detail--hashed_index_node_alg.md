# #59 - C2039: 'is_first_of_group': is not a member of 'boost::multi_index::detail::hashed_index_node_alg [Closed]

> Username: playgithub  
> Created at: 2022-04-17 06:29:08 UTC  
> Updated at: 2022-04-19 13:50:10 UTC  
> Closed at: 2022-04-19 13:50:10 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59  

## Env  
Windows  
Visual Studio 2022  
boost 1.78.0  
vcpkg  
C++ 17  
  
## Compile Error  
```  
C2039: 'is_first_of_group': is not a member of 'boost::multi_index::detail::hashed_index_node_alg  
```  
  
## Source Code  
https://github.com/boostorg/multi_index/blob/9c40dfee82e3c42960e137cd765d7bcb850dc256/include/boost/multi_index/hashed_index.hpp#L1598

---

## Comment 1

> Username: joaquintides  
> Created at: 2022-04-17 14:50:55 UTC  
> Updated at: 2022-04-17 14:51:28 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1100894358  

Hi, thanks for the report. I'd need more info to be able to reproduce the error, as tests here for VS 2022 run OK (look for "msvc- 14.3" at https://www.boost.org/development/tests/master/developer/multi_index.html).  
  
- Can you provide more info on the environment where you've found the problem?  
- Can you provide a link to the code using Boost.MultiIndex that triggered the compiler error? Even better, can you provide a minimal, complete and verifiable example code showing the problem?  
  
Thank you,

---

## Comment 2

> Username: playgithub  
> Created at: 2022-04-17 16:28:59 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1100910986  

> Hi, thanks for the report. I'd need more info to be able to reproduce the error, as tests here for VS 2022 run OK (look for "msvc- 14.3" at https://www.boost.org/development/tests/master/developer/multi_index.html).  
>   
> * Can you provide more info on the environment where you've found the problem?  
  
* VS2022 with msvc 14.3  
* Qt 6.3.0  
* Qt Creator 7.0.0  
* install boost by `conan` or `vcpkg` (both the same error)  
  
Strangely, in Visual Studio 2022 solution which also use `vcpkg`, it builds successfully.  
  
  
> * Can you provide a link to the code using Boost.MultiIndex that triggered the compiler error? Even better, can you provide a minimal, complete and verifiable example code showing the problem?  
  
I've written a simple project, but it can't be reproduced, it builds successfully.  
  
It might be a bug for me, so close the issue first, if it is for me, I should reopen it with enough info.  
  
Thanks

---

## Comment 3

> Username: joaquintides  
> Created at: 2022-04-17 17:17:44 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1100918611  

Ok, good luck with your project.

---

## Comment 4

> Username: joaquintides  
> Created at: 2022-04-17 19:31:06 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1100937065  

Hi Ronald  
  
I received a notification of some additional comment of yours on this issue, but the comment is not appearing online now, maybe you've deleted it?

---

## Comment 5

> Username: playgithub  
> Created at: 2022-04-18 03:36:55 UTC  
> Updated at: 2022-04-18 03:39:00 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1101060846  

> I received a notification of some additional comment of yours on this issue, but the comment is not appearing online now, maybe you've deleted it?  
  
Yes, when using it with  
* vcpkg  
* cmake  
* Qt Creator  
  
it doesn't work, but when using it with  
* vcpkg  
* Visual Studio Solution  
* Visual Studio 2022  
  
it works  
  
So I'm not sure  what's the problem.  
  
I'm going to test the former case on Linux.

---

## Comment 6

> Username: joaquintides  
> Created at: 2022-04-18 10:12:46 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1101287195  

In a comment that didn't make it online (yet I received via a notification email) you talked about a `sample.cpp` file. Would it be possible to take a look at that?

---

## Comment 7

> Username: playgithub  
> Created at: 2022-04-18 10:29:38 UTC  
> Updated at: 2022-04-18 10:30:12 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1101297136  

> In a comment that didn't make it online (yet I received via a notification email) you talked about a `sample.cpp` file. Would it be possible to take a look at that?  
  
`sample.cpp` is just a placeholder , I've tried to write a simple demo, but it can't be reproduced.  
BTW, I've tried linux, but it got another problems when do `conan install` for boost.  
Anyway I'm going to debug it on Windows.  
If I find s.th. helpful, I'll post it here.

---

## Comment 8

> Username: playgithub  
> Created at: 2022-04-18 16:10:51 UTC  
> Updated at: 2022-04-19 12:58:08 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1101530977  

The problem is caused by `__declspec(dllexport)`, if comment it out, there will be no problem.  
Anyway to export a class derived from `bmi::multi_index_container` on Windows?  
  
```cpp  
#include <memory>  
#include <string>  
  
#include <boost/uuid/uuid.hpp>  
#include <boost/multi_index_container.hpp>  
#include <boost/multi_index/hashed_index.hpp>  
#include <boost/multi_index/random_access_index.hpp>  
#include <boost/multi_index/mem_fun.hpp>  
  
  
namespace bmi = boost::multi_index;  
  
class A  
{  
public:  
    A(int id, const std::string& name) : __id(id), __name(name) {}  
public:  
    int GetID() const { return __id; }  
    const std::string& GetName() const { return __name; }  
private:  
    int __id;  
    std::string __name;  
};  
  
struct random_access_index_tag;  
struct id_index_tag;  
struct name_index_tag;  
  
typedef bmi::multi_index_container<  
    std::shared_ptr<A>,  
    bmi::indexed_by<  
    bmi::random_access<bmi::tag<random_access_index_tag>>,  
    bmi::hashed_unique<bmi::tag<id_index_tag>, bmi::const_mem_fun<A, int, &A::GetID>>,  
    bmi::hashed_unique<bmi::tag<name_index_tag>, bmi::const_mem_fun<A, const std::string&, &A::GetName>>  
    >  
> AMultiIndexContainer;  
  
class __declspec(dllexport) AGroup : public AMultiIndexContainer  
{  
};  
```

---

## Comment 9

> Username: playgithub  
> Created at: 2022-04-18 16:56:33 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1101563866  

In the post https://stackoverflow.com/a/7302165, it says "C++ is not a "pure OOP" and container are not OOP at all.".  
Does it mean that in this case it is not well supported to derive from `boost::multi_index`?

---

## Comment 10

> Username: joaquintides  
> Created at: 2022-04-18 18:35:27 UTC  
> Updated at: 2022-04-18 18:54:30 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1101640570  

Your problem has nothing to do with deriving from `multi_index_container` (which some may find questionable but is undoubtedly doable). What I think it's happening is the following:  
- Exporting a class template specialization, as you're doing here via export of a derived class, exports all its non-template member functions (such as `begin`, `end`, or `insert`) and none of its  member function *templates* (`find`, `emplace`, etc.) So far so good, you'll have duplicates of member function templates in your EXE and your DLL but that's probably OK.  
- Unfortunately, all the non-template *private* functions will be exported as well. `hash_index` has two versions of the non-template member function `in_place`, one for unique indices and the other for non-unique indices, which are selected at compile time based on the uniqueness of the index. But the compiler is exporting and, consequently, trying to compile *both*, and the non-unique version fails because, well, the index is unique.  
- My suggested solution is then to stop exporting the whole `AGroup` class and instead export the member functions of  `AGroup` individually. This won't export any of the `AMultiIndexContainer` member functions, but that shouldn't be a problem except for some code duplication in the EXE and the DLL (your EXE will be bigger).

---

## Comment 11

> Username: playgithub  
> Created at: 2022-04-19 07:52:13 UTC  
> Updated at: 2022-04-19 07:52:24 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1102231328  

So the problem dues to the functions below when exporting  
  
```cpp  
bool in_place(  
    node_impl_pointer x,key_param_type k,std::size_t buc,  
    hashed_unique_tag)const  
```  
  
```cpp  
  bool in_place(  
    node_impl_pointer x,key_param_type k,std::size_t buc,  
    hashed_non_unique_tag)const  
```  
  
> But the compiler is exporting and, consequently, trying to compile both, and the non-unique version fails because, well, the index is unique.  
  
I don't quite understand this, does it mean they have the same function signature to the complier,  
and only one can be exported when exporting?  
And for static link, it simply works by specializing the template with different template param `Category`, right?

---

## Comment 12

> Username: joaquintides  
> Created at: 2022-04-19 09:02:47 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1102343190  

It doesn't have to do with the signature, these two are different functions and the problem is that the compiler tries to generate both. Let me explain by way of an example:  
  
```cpp  
template<typename T>  
struct AClassTemplate  
{  
  static void call_foo(){T::foo();}  
  static void call_bar(){T::bar();}  
};  
  
struct FooClass  
{  
  static void foo(){}  
};  
  
struct __declspec(dllexport) DerivedClass:AClassTemplate<FooClass>  
{  
  void call_foo(){AClassTemplate<FooClass>::call_foo();}  
};  
  
int main(){}  
```  
This [fails](https://godbolt.org/z/xY97657b9) because the compiler is trying to export (and compile) `AClassTemplate<FooClass>::call_foo` (OK) and `AClassTemplate<FooClass>::call_bar` (NOK, `FooClass` has no `bar`member function to call). On the contrary, the following:  
  
```cpp  
template<typename T>  
struct AClassTemplate  
{  
  static void call_foo(){T::foo();}  
  static void call_bar(){T::bar();}  
};  
  
struct FooClass  
{  
  static void foo(){}  
};  
  
struct DerivedClass:AClassTemplate<FooClass>  
{  
  void __declspec(dllexport) call_foo(){AClassTemplate<FooClass>::call_foo();}  
};  
  
int main(){}  
```  
[works](https://godbolt.org/z/7xrqx3MT6) because we are not compiling every member function of `AClassTemplate<FooClass>`, but only those used by the exported function `DerivedClass::call_foo`. Clearer now?

---

## Comment 13

> Username: playgithub  
> Created at: 2022-04-19 13:50:10 UTC  
> Url: https://github.com/boostorg/multi_index/issues/59#issuecomment-1102681516  

Yes, clear now, because specialization of a template is not complete.  
Thanks very much
