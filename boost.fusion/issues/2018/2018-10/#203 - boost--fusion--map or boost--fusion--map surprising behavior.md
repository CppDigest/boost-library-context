# #203 - boost::fusion::map or boost::fusion::map surprising behavior [Open]

> Username: arximboldi  
> Created at: 2018-10-23 14:27:30 UTC  
> Updated at: 2018-10-23 14:41:09 UTC  
> Url: https://github.com/boostorg/fusion/issues/203  

Hi!  
  
I found surprising behavior with `boost::fusion::map`.   
Given the following set of types:  
```cpp  
struct Foo {};  
struct Bar {};  
using Child = boost::fusion::map<boost::fusion::pair<Foo, int>>;  
using Parent = boost::fusion::map<boost::fusion::pair<Bar, Child>>;  
```  
The following example fails to compile:  
```cpp  
auto example()  
{  
    return Parent{  
        boost::fusion::make_pair<Bar>(  
                boost::fusion::make_pair<Foo>(42))};  
}  
```  
This can be fixed by doing:  
```cpp  
auto example()  
{  
    return Parent{  
        boost::fusion::pair<Bar, Child>(  
                boost::fusion::make_pair<Foo>(42))};  
}  
```  
While I do understand while the former fails and the latter doesn't. I do find this suprising behavior. Specially, considering the errors produced by the failing example.

---

## Comment 1

> Username: arximboldi  
> Created at: 2018-10-23 14:33:19 UTC  
> Url: https://github.com/boostorg/fusion/issues/203#issuecomment-432272110  

There are even more confusing examples I believe (trying to disentangle my metaprograming to produce a minimal example...)

---

## Comment 2

> Username: arximboldi  
> Created at: 2018-10-23 14:40:59 UTC  
> Updated at: 2018-10-23 14:41:09 UTC  
> Url: https://github.com/boostorg/fusion/issues/203#issuecomment-432275241  

Ahaaa!  Found it, the problem arises when the nested map is not the first element of the outer map. The problem seems to be related to how `map_impl` works via recursive inheritance in combination with some broken SFINAE. Here is a failing example that should clearly not fail...  
```cpp  
struct Foo {};  
struct Bar {};  
using Child = boost::fusion::map<boost::fusion::pair<Foo, int>>;  
using Parent = boost::fusion::map<  
    boost::fusion::pair<Foo, float>,  
    boost::fusion::pair<Bar, Child>>;  
  
auto example()  
{  
    return Parent{  
        boost::fusion::pair<Foo, float>{42.0f},  
        boost::fusion::pair<Bar, Child>{Child{  
                boost::fusion::pair<Foo, int>{42}}}};  
}  
```
