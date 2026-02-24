# #133 - non-constexpr integral_constant conversions [Closed]

> Username: CaseyCarter  
> Created at: 2019-11-12 18:57:46 UTC  
> Updated at: 2020-01-14 09:38:54 UTC  
> Closed at: 2020-01-14 09:38:54 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133  

`integral_constant` has [this conversion operator to `mpl::integral_c`](https://github.com/boostorg/type_traits/blob/develop/include/boost/type_traits/integral_constant.hpp#L60-L66):  
```c++  
operator const mpl::integral_c<T, val>& ()const  
{  
    static const char data[sizeof(long)] = { 0 };  
    static const void* pdata = data;  
    return *(reinterpret_cast<const mpl::integral_c<T, val>*>(pdata));  
}  
```  
which unfortunately is not / cannot be `constexpr`.  (The `integral_constant<bool, meow>` partial specialization has [a similar conversion to lvalue `mpl::bool_<meow>`](https://github.com/boostorg/type_traits/blob/develop/include/boost/type_traits/integral_constant.hpp#L80-L85).) These conversions are used in the initialization of lots of constant data in Boost::math, which could otherwise have constant initialization but sadly currently has dynamic initialization since these conversions are not `constexpr`.  
  
Is there any way these could be made `constexpr` conversions to avoid that dynamic initialization?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-12 19:29:39 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133#issuecomment-553076620  

Yes I know :(  
  
This was a solution we came up with to avoid type_traits depending on MPL.  And indeed it works well, but as you correctly point out the function *cannot* be `constexpr` which is a serious issue.  
  
I'm open to ideas as at present I don't know how to fix this!

---

## Comment 2

> Username: CaseyCarter  
> Created at: 2019-11-12 19:48:09 UTC  
> Updated at: 2019-11-12 19:48:32 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133#issuecomment-553084374  

IIUC, the goal is to allow `mpl::integral_c` / `mpl::bool_` to be incomplete when definining `integral_constant`? Is the "lvalueness" of the conversion operators also significant? If not, something like (https://godbolt.org/z/JHKfSm):  
```c++  
template<class T, T> struct integral_c;  
  
template<class T, T val>  
struct integral_constant {  
    constexpr operator integral_c<T, val>() const noexcept {  
        return {};  
    }  
};  
  
constexpr integral_constant<int, 42> i42;  
  
template<class T, T> struct integral_c {};  
  
int main() {  
    integral_c<int, 42>(i42);  
}  
```  
works, since return types need be complete when instantiating the body, but not the declaration, of a function. I assume that requiring completeness when instantiating the body isn't a huge bar, since presumably the caller wants to actually *do* something with the returned object. But this seems simple enough that it would have occurred to someone. Is there a requirement I'm missing?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-11-13 19:51:02 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133#issuecomment-553572690  

Nearly:  we have tests which rely on dispatching without explicitly including MPL, for example this one https://github.com/boostorg/type_traits/blob/develop/test/mpl_interop_test1.cpp fails with your change.  
  
I experimented with:  
  
```  
      constexpr operator const mpl::integral_c<T, val>& ()const  
      {  
         return *static_cast<const mpl::integral_c<T, val>*>(nullptr);  
      }  
```  
  
Which passes all the type_traits tests, but breaks Boost.Test which manages to dereference the returned value, but only with MSVC :(

---

## Comment 4

> Username: CaseyCarter  
> Created at: 2019-11-13 21:01:44 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133#issuecomment-553600539  

If this conversion must be callable when `mpl::integral_c` is incomplete then your proposal - whose return value cannot undergo lvalue-to-rvalue conversion - may be the best we can do. "This type needs to have a dependency on this other type but without having an actual dependency" is a peculiar requirement, but I'll accept on faith that there are good reasons for it that I don't want to invest the time to properly understand.  
  
> but breaks Boost.Test which manages to dereference the returned value, but only with MSVC :(  
  
This is likely a case of MSVC's `constexpr` evaluator performing lvalue-to-rvalue conversion when it should not: we have a few bugs to that effect IIRC. I'll dig into this and see if we can get the compiler fixed to unblock this change so we don't have to continue patching it.  
  
(Related: I haven't had the opportunity to thank you in person for Boost::Math, which we use to implement the C++17 mathematical special functions in MSVC. Here's a virtual Thank You, which will have to do for now.)

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-11-14 12:12:55 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133#issuecomment-553861150  

>"This type needs to have a dependency on this other type but without having an actual dependency" is a peculiar requirement,  
  
Indeed: it's legacy really, there was never supposed to be any dependency, but to work around broken compilers (which we no longer support), MPL needed to inject a bunch of code into type_traits.  Over time a lot of code came to depend on the traits inheriting from mpl::true_ or mpl::false_ - mostly due to convenience.  So... when we removed the no longer needed workarounds, the conversion operators were added to avoid breaking too much other stuff.  
  
So... probably the right way to fix this is to stop relying on the conversion in Boost.Math.  It's a rather tedious bit of editing, so it may have to wait for a day when I'm really properly bored ;)

---

## Comment 6

> Username: Kojoley  
> Created at: 2019-11-20 19:07:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/133#issuecomment-556258678  

I was looking into the issue a while ago when I was trying to make `boost::integral_constant`/`boost::mpl::integral_c`/`boost::mpl::bool_` convertible to `std::integral_constant` for make transition to standard type traits easier, but I end up with nothing, except the bug in MSVC that prevents the main idea https://developercommunity.visualstudio.com/content/problem/417059/fails-to-compile-valid-template-template-parameter.html, and also discovered that this:  
```cpp  
      BOOST_CONSTEXPR operator mpl::integral_c<T, val>() const  
      { return mpl::integral_c<T, val>(); }  
...  
      BOOST_CONSTEXPR operator mpl::bool_<val>() const  
      { return mpl::bool_<val>(); }  
```  
works fine on all three major compilers. I do not know if this is actually allowed, [DR2335](http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_active.html#2335) seems to be about this uncertainty.
