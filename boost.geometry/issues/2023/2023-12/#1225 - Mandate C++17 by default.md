# #1225 - Mandate C++17 by default [Closed]

> Username: georgthegreat  
> Created at: 2023-12-18 13:06:10 UTC  
> Updated at: 2023-12-19 15:17:53 UTC  
> Closed at: 2023-12-19 15:17:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225  

Hi.  
  
At the time boost/geometry mandates C++14 as its default C++ standard.  
I wonder if there is a will for raising the bar to C++17.  
  
My rationale is as follows: C++17 introduces std::variant, and boost::geometry is the only client of boost:::variant2 in all our codebase.  
Maybe we can switch and remove the dependency?

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-12-18 17:06:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225#issuecomment-1861076938  

Hi!  
  
No, we cannot do that, at least not yet, there are still customers using C++14  
  
Kind regards, Barend

---

## Comment 2

> Username: awulkiew  
> Created at: 2023-12-18 18:56:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225#issuecomment-1861341400  

@georgthegreat This dependency is optional. As long as you don't explicitly include `<boost/geometry/geometries/adapted/boost_variant2.hpp>` you can remove it from your codebase. Unless I'm missing something.

---

## Comment 3

> Username: awulkiew  
> Created at: 2023-12-18 19:00:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225#issuecomment-1861349281  

If you want to use `std::variant` you can include `<include/boost/geometry/geometries/adapted/std_variant.hpp>`. Even if we required C++17 we wouldn't drop this optional dependency the same way we didn't drop the support for `boost::variant` when we moved to C++14.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-12-18 22:37:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225#issuecomment-1861801867  

@awulkiew thanks for the details. Okay, so if I understand it correctly, we just drop adaptation to variant2 and the internal code does not depend on it. That is fine.

---

## Comment 5

> Username: vissarion  
> Created at: 2023-12-19 08:43:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225#issuecomment-1862343999  

Thanks @awulkiew, indeed `boost::variant2` is only included in tests thus an optional dependency. However, `boost::variant` is still a real dependency (for backward compatibility) since `<include/boost/geometry/geometries/adapted/boost_variant.hpp>` is included by (almost) all boost geometry algorithms.

---

## Comment 6

> Username: georgthegreat  
> Created at: 2023-12-19 15:17:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1225#issuecomment-1862964484  

Ok, thanks everyone for explaining this to me.
