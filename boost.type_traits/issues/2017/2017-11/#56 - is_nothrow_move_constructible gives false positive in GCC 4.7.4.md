# #56 - is_nothrow_move_constructible gives false positive in GCC 4.7.4 [Closed]

> Username: akrzemi1  
> Created at: 2017-11-08 10:55:11 UTC  
> Updated at: 2018-05-07 18:52:27 UTC  
> Closed at: 2018-05-07 18:52:27 UTC  
> Url: https://github.com/boostorg/type_traits/issues/56  

The following code assertion-fails on GCC 4.7:  
  
```c++  
#include <boost/type_traits.hpp>  
  
struct X  
{  
  X(X&&) noexcept(false) { throw 0; }  
};  
  
X&& make_X() noexcept;  
  
int main()   
{  
  static_assert(!boost::is_nothrow_move_constructible<X>::value, "** 1");  
  static_assert(!noexcept(X(make_X())), "** 2");  
}  
```  
  
(see the failure online at https://wandbox.org/permlink/mTBxqXQED1ts34uF)  
  
This is because the trait is expanded to:  
  
```c++  
template <class T>  
struct is_nothrow_move_constructible  
   : public integral_constant<bool,  
   (::boost::has_trivial_move_constructor<T>::value || ::boost::has_nothrow_copy<T>::value) && !::boost::is_array<T>::value>  
{};  
```  
And `boost::has_nothrow_copy<T>::value` works incorrectly on this compiler:  
  
```c++  
#include <boost/type_traits.hpp>  
  
struct X  
{  
  X(X&&) = delete;  
};  
  
int main()   
{      
  static_assert(!::boost::has_nothrow_copy<X>::value, "** 2");  
}  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-02-01 19:24:25 UTC  
> Url: https://github.com/boostorg/type_traits/issues/56#issuecomment-362374121  

I have no workaround for this: the standard conforming version of the code is explicitly disabled for GCC < 4.8 due to other compiler bugs breaking stuff.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-02-01 20:25:36 UTC  
> Url: https://github.com/boostorg/type_traits/issues/56#issuecomment-362391092  

Maybe it would be possible to define a sibling trait, say, `boost::strict::is_nothrow_move_constructible`, which would return `false` unless you are absolutely sure that the `true` answer is standard-compliant?

---

## Comment 3

> Username: Dushistov  
> Created at: 2018-03-07 00:27:09 UTC  
> Url: https://github.com/boostorg/type_traits/issues/56#issuecomment-370978538  

Hm, looks like I has the same issue here: boostorg/variant#47 : can not use move only type inside `boost::variant` with gcc 4.7.2

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-05-07 18:52:27 UTC  
> Url: https://github.com/boostorg/type_traits/issues/56#issuecomment-387165656  

I think I have this mostly fixed: msvc-12.0 has the same issue and fails.  GCC-4.4 is totally broken with this test case.  But GCC-4.6 and higher should be OK.
