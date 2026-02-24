# #148 - core/swap.hpp(81,1): fatal error C1202: recursive type or function dependency context too complex [Closed]

> Username: afabri  
> Created at: 2023-07-05 14:17:16 UTC  
> Updated at: 2023-08-25 11:50:08 UTC  
> Closed at: 2023-08-25 11:50:07 UTC  
> Url: https://github.com/boostorg/core/issues/148  

We get a ` boost_1_82_0\boost/core/swap.hpp(81,1): fatal error C1202: recursive type or function dependency context too complex`  when compiling [this program](https://gist.github.com/afabri/1d772a2807459ad07e85450f6c7477af)  with VC2019 or VC2022 and stdc++17 enabled.     [link](https://github.com/boostorg/core/blob/develop/include/boost/core/swap.hpp#L81)  
Neither g++ nor clang have a problem.   
The code is nonsensical and we only boiled down the real code to make it a minimal example. Also ` #include <boost/swap.hpp>`  is not used but only needed for getting the error.   
Maybe this is more a VC++ issue than a boost issue.

---

## Comment 1

> Username: afabri  
> Created at: 2023-07-07 12:48:38 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1625367899  

Hi @Lastique   
Do you by any chance have access to the VC++ compiler?  I ask because your commit concerning the noexception triggers the problem.

---

## Comment 2

> Username: Lastique  
> Created at: 2023-07-07 13:43:41 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1625437712  

> Do you by any chance have access to the VC++ compiler?  
  
Not at the moment or in the coming few weeks.  
  
If you are willing to try something, you could try the following:  
  
1. Envelop `boost::swap` definition [here](https://github.com/boostorg/core/blob/216999e552e7f73e63c7bcc88b8ce9c179bbdbe2/include/boost/core/swap.hpp#L77-L84) in a new namespace within namespace `boost`, say `namespace swap_adl_block`.  
2. After that namespace closes, import `boost::swap_adl_block::swap` into namespace `boost` by adding `using swap_adl_block::swap` declaration.  
3. Try if it fixes your problem.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-07-09 11:46:44 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627691786  

Reduced example:  
  
```  
#include <boost/swap.hpp> // compiles if removed  
#include <boost/checked_delete.hpp>  
  
template<class T> class X  
{  
    X( X const& );  
  
public:  
  
    X() {}  
};  
  
int main()  
{  
    X< boost::checked_deleter<int> > x;  
    return noexcept( swap( x, x ) );  
}  
```  
  
Fails under all compilers (e.g. https://godbolt.org/z/ea65PWz9b). The reason is that `std::swap` is SFINAEd away when the type isn't move constructible, so the unqualified `swap` call ends up calling `boost::swap` recursively, forever.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-07-09 17:28:23 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627778099  

Why did we need the change in https://github.com/boostorg/core/commit/8a8738a981e1d46146939f93ac0d751b827e7704?

---

## Comment 5

> Username: Lastique  
> Created at: 2023-07-09 19:54:35 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627809194  

On July 9, 2023 8:28:34 PM Peter Dimov ***@***.***> wrote:  
  
> Why did we need the change in   
> https://github.com/boostorg/core/commit/8a8738a981e1d46146939f93ac0d751b827e7704?  
  
This reflects std::swap. I also needed this in some of my code.  
  
Transparently noexcept swap is the right thing to do. The fix should be to   
prevent boost::swap from being found by ADL.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-07-09 20:36:50 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627818812  

To prevent `boost::swap` from being found by ADL, we'll have to make it a function object. That might be the right thing to do, but it's a significant change and might have other unintended consequences.

---

## Comment 7

> Username: Lastique  
> Created at: 2023-07-09 22:17:58 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627841906  

> Reduced example:  
>   
> ```  
> #include <boost/swap.hpp> // compiles if removed  
> #include <boost/checked_delete.hpp>  
>   
> template<class T> class X  
> {  
>     X( X const& );  
>   
> public:  
>   
>     X() {}  
> };  
>   
> int main()  
> {  
>     X< boost::checked_deleter<int> > x;  
>     return noexcept( swap( x, x ) );  
> }  
> ```  
>   
> Fails under all compilers (e.g. https://godbolt.org/z/ea65PWz9b). The reason is that `std::swap` is SFINAEd away when the type isn't move constructible, so the unqualified `swap` call ends up calling `boost::swap` recursively, forever.  
  
This code is incorrect either way. If `boost::swap` is found via ADL, the call is recursive and fails due to `noexcept`. Before the addition of `noexcept`, it would compile into an infinite recursion in runtime. If the `boost::swap` is not found via ADL, then there is no `swap` that accepts `X` - `std::swap` requires the type to be copyable.  
  
I'm not sure this repro is equivalent to the code posted by the OP. Their code involves `std::tuple`, and without seeing its implementation I can't tell what triggers `swap` lookup and why it fails specifically on MSVC and not other compilers. I'm assuming, the OP's code compiles without `noexcept` specification and fails with it.

---

## Comment 8

> Username: Lastique  
> Created at: 2023-07-09 22:25:49 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627843765  

@afabri Could you check if 987ffb3 resolves the problem for you?

---

## Comment 9

> Username: Lastique  
> Created at: 2023-07-09 22:30:43 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627845026  

> To prevent `boost::swap` from being found by ADL, we'll have to make it a function object.  
  
Not necessarily, a using directive would do.

---

## Comment 10

> Username: pdimov  
> Created at: 2023-07-09 22:33:12 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627845727  

My guess is that it uses `std::is_nothrow_swappable` somewhere.  
  
> Not necessarily, a using directive would do.  
  
I don't think it would.

---

## Comment 11

> Username: Lastique  
> Created at: 2023-07-09 23:24:01 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627859174  

> > Not necessarily, a using directive would do.  
>   
> I don't think it would.  
  
It does work in my local testing, but it turns out it has unexpected side effect that shows as `swap_std_vector_of_boost` test failure. Basically, if there is an overload of `boost::swap` for some type (besides Boost.Swap) then the `boost::swap` from Boost.Swap is no longer found, even if that overload is not suitable for the call arguments. I believe, we would have had the same problem with the function object.  
  
I don't see a way around this. Would we be willing to ban `swap` overloads in namespace `boost` other than Boost.Swap, Boost-wide?

---

## Comment 12

> Username: pdimov  
> Created at: 2023-07-09 23:56:48 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1627866813  

> Would we be willing to ban `swap` overloads in namespace `boost` other than Boost.Swap, Boost-wide?  
  
I don't think we can do that. Types defined in namespace `boost` have to have their `swap` functions defined there as well.

---

## Comment 13

> Username: Lastique  
> Created at: 2023-07-10 07:53:47 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1628426475  

> > Would we be willing to ban `swap` overloads in namespace `boost` other than Boost.Swap, Boost-wide?  
>   
> I don't think we can do that. Types defined in namespace `boost` have to have their `swap` functions defined there as well.  
  
The idea was to move those types to their own namespaces within `boost` and then import them into `boost` with using declarations. But maybe that's too much to ask of maintainers.  
  
Another solution would be to rename `swap` from Boost.Swap e.g. to `adl_swap` or move it to a namespace of its own (any naming suggestions?). For backward compatibility, we would keep the `boost::swap` name available (as a forwarding function or a using declaration) but marked as deprecated, which we would eventually remove. The problem would persist until we remove it.

---

## Comment 14

> Username: pdimov  
> Created at: 2023-07-10 08:53:19 UTC  
> Url: https://github.com/boostorg/core/issues/148#issuecomment-1628526973  

We can add `adl_swap` or whatever with proper noexcept forwarding, and leave boost::swap as it was (in addition to deprecating it) to avoid the regression.  
  
`boost::core::swap` would be the obvious candidate but we can't do that because there are types in `boost::core` for which it will be found by ADL. So maybe just changing the name to something other than `swap` would be best. Maybe `swap_adl` or `invoke_swap`. (Or even `boost::core::invoke_swap`.)
