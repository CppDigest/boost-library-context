# #173 - Regression when using recursive_wrapper<small_vector<incomplete, 4>> [Closed]

> Username: pdimov  
> Created at: 2020-12-19 15:01:32 UTC  
> Updated at: 2021-04-21 08:34:05 UTC  
> Closed at: 2021-04-21 08:34:05 UTC  
> Url: https://github.com/boostorg/container/issues/173  

As reported on Slack, the following code  
```  
#include <boost/variant.hpp>  
#include <boost/container/small_vector.hpp>  
  
struct variable;  
  
using table_value = boost::container::small_vector<variable, 4>;  
  
using value_type = boost::variant<int, boost::recursive_wrapper<table_value>>;  
  
struct __declspec(dllexport) variable  
{  
	value_type value_;  
};  
  
int main()  
{  
}  
```  
doesn't compile under MSVC 2019 (see https://github.com/KazDragon/telnetpp/issues/238), but is claimed to have worked with 1.74. The issue doesn't reproduce if I use `recursive_wrapper<variable>` directly, without the `small_vector`, and `recursive_wrapper` hasn't changed since 1.74, so it must be a change in `small_vector` that has triggered the failure.

---

## Comment 1

> Username: KazDragon  
> Created at: 2020-12-19 15:04:47 UTC  
> Updated at: 2020-12-19 15:05:00 UTC  
> Url: https://github.com/boostorg/container/issues/173#issuecomment-748485939  

https://ci.appveyor.com/project/KazDragon/telnetpp/builds/36920601/job/6ae7oxa6taxsvivt is a build using code of that layout when building a DLL on MSVC with Boost 1.74.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2020-12-29 14:27:37 UTC  
> Updated at: 2020-12-29 14:30:29 UTC  
> Url: https://github.com/boostorg/container/issues/173#issuecomment-752096105  

I don't know how this was supposed to work with small_vector, as small_vector is not supposed to work with incomplete types (small_vector needs to store variables internally so it needs to know the size and the alignment, which requires a fully defined type).  
  
In the example, due to the DLL atribute; value_ is fully instantiated, which somehow requires small_vector<variable>to be instantiated. The instantiation chain suggests that boost::is_nothrow_move_assignable is called for boost::recursive_wrapper<table_value> which triggers the incomplete small_vector instantiation for small_vector<variable>.  
  
In the example, removing the dllexport attribute makes the error go away, but I can't figure out why dllexport changes the rules. The issue starts in the BOOST_NOEXCEPT_IF part of variant that triggers the instantiation of small_vector<variable,> while variable is still not fully defined:  
```  
#ifndef BOOST_NO_CXX11_RVALUE_REFERENCES  
    variant& operator=(variant&& rhs)  
#if !defined(__GNUC__) || (__GNUC__ != 4) || (__GNUC_MINOR__ > 6) || defined(__clang__)  
        BOOST_NOEXCEPT_IF(variant_move_noexcept_constructible::type::value && variant_move_noexcept_assignable::type::value)  
#endif  
    {  
        variant_assign( detail::variant::move(rhs) );  
        return *this;  
    }

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-12-30 08:58:15 UTC  
> Url: https://github.com/boostorg/container/issues/173#issuecomment-752380849  

Further experiments suggest that it is related to the implicit generation of copy/move constructor/assignment operations, as this works fine:  
  
```  
struct __declspec(dllexport) variable  
{  
   variable (const variable &o)  : value_(o.value_)  {}  
   variable (variable &&o)         : value_(boost::move(o.value_))  {}  
  
   variable & operator=(const variable &o)  
   {    value_ = o.value_;   return *this;    }  
  
   variable & operator=(variable &&o)  
   {   value_ = boost::move(o.value_);   return *this;   }  
  
   value_type value_;  
};  
  
I am unsure this is a `small_vector` issue.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2021-04-21 08:34:05 UTC  
> Url: https://github.com/boostorg/container/issues/173#issuecomment-823885506  

Reviewing the issue I think it's not a Boost.Container error.  
  
small_vector is not supposed to support incomplete types and somehow using __declspec(dllexport)  provokes early instantiation of small_vector.  
  
I understand that this can be viewed as a regression, but I think code worked accidentally and I don't want to support those corner cases as any change in small_vector could trigger similar errors.  
  
Maybe this could indicate a compiler issue since removing dllexport fixes the compilation error.
