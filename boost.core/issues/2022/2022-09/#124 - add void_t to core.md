# #124 - add void_t to core [Closed]

> Username: HDembinski  
> Created at: 2022-09-17 14:40:23 UTC  
> Updated at: 2023-02-06 14:40:58 UTC  
> Closed at: 2023-02-04 00:16:24 UTC  
> Url: https://github.com/boostorg/core/issues/124  

The following variadic template exists in hana, beast, json, describe, math, histogram, mp11, and probably many other boost libraries:  
```c++  
template <typename ...>  
using void_t = void;  
```  
Please add it to core. I can draft a PR if this is desired.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-09-17 15:22:57 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1250090038  

Why not Boost.TypeTraits?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-09-17 16:36:35 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1250101706  

It's already there. https://github.com/boostorg/type_traits/blob/develop/include/boost/type_traits/make_void.hpp

---

## Comment 3

> Username: Lastique  
> Created at: 2022-09-17 18:15:24 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1250117885  

Then I suppose we can close this.

---

## Comment 4

> Username: HDembinski  
> Created at: 2022-10-02 10:42:31 UTC  
> Updated at: 2022-10-02 10:44:07 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1264610195  

Please reopen. Core is supposed to provide basic types used throughout Boost. It exists so that we can reduce internal coupling of Boost libraries. Boost Histogram is not using Boost type_traits, it is using std type_traits for that reason. Hence I cannot import void_t from there, it has to be in core.

---

## Comment 5

> Username: Lastique  
> Created at: 2022-10-02 11:52:48 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1264624820  

On October 2, 2022 1:42:42 PM Hans Dembinski ***@***.***> wrote:  
  
> Please reopen. Core is supposed to provide basic types used throughout   
> Boost. It exists so that we do not have to reduce internal coupling of   
> Boost libraries. Boost Histogram is not using Boost type_traits, it is   
> using std type_traits. Hence I cannot import void_t from there.  
  
I don't think this is a valid reason to duplicate code in Core. We'll end   
up with everything in Core this way.  
  
If you don't want to rely on Boost.TypeTraits then you'll have to implement   
void_t in Histogram. Gladly, it's not difficult. But frankly, I don't see   
the problem. You're already ok to depend on Core, and TypeTraits doesn't   
add more dependencies, IIRC.

---

## Comment 6

> Username: pdimov  
> Created at: 2022-10-02 13:51:18 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1264649035  

I agree that we shouldn't be duplicating TypeTraits in Core. TypeTraits used to be a part of a dependency cycle with MPL, Utility, and other stuff, but these days are long past and it only depends on Config and StaticAssert nowadays.

---

## Comment 7

> Username: HDembinski  
> Created at: 2022-10-04 18:05:54 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267367679  

I don't want type_traits in core, just void_t. Having one dependency less for Histogram is better than having one more. It is simple. Yes, I can and have implemented void_t myself, but we have types in core that are equally trivial, boost::type for instance.

---

## Comment 8

> Username: HDembinski  
> Created at: 2022-10-04 18:08:26 UTC  
> Updated at: 2022-10-04 18:10:02 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267370194  

Perhaps you could formulate the rules precisely for what should be adopted in core and what not, because I am getting the impression that it is arbitrary. Please reopen.

---

## Comment 9

> Username: pdimov  
> Created at: 2022-10-04 18:16:09 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267377909  

`boost::type` is a legacy, from the time we moved things into Core in order to break library cycles. `enable_if` exists here for the same reason. Now that TypeTraits has `enable_if`, I even prefer to use that instead of the Core one.

---

## Comment 10

> Username: pdimov  
> Created at: 2022-10-04 18:19:00 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267380823  

If you so insist on having `void_t` in Core, please do a PR (with documentation and tests) and then argue your case on the list. I'm not going to switch either Describe or Mp11 to it, and Math is not going to switch either, but the rest of the libraries might.

---

## Comment 11

> Username: Lastique  
> Created at: 2022-10-04 21:13:30 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267584797  

> Perhaps you could formulate the rules precisely for what should be adopted in core and what not, because I am getting the impression that it is arbitrary.  
  
From the start, Core was Utility, only with less dependencies - this was one of the reasons Core was created. This is about as formal as you'll get. Yes, inclusion is somewhat arbitrary, but there should be strong reasons for the component to be in Core and not somewhere else, especially when there is a library where the component fits perfectly, doubly so when it already exists there. IMO, dropping exactly one dependency is not that reason. I could understand if the choice was between having zero Boost dependencies and one, but that's not the case (and having `void_t` in Core wouldn't have solved it anyway).  
  
You want `void_t`, someone else wants `is_same` (and despite my protest it was added), someone third will want `remove_cv` or `remove_reference` because these are universally used. Then someone wants `unique_ptr`, `shared_ptr` and whatnot because those are basic vocabulary types, right? You see where this is going? I'm strongly against this. We have dedicated libraries for this, use those. Deal with dependencies. Or don't, and implement everything you need locally.  
  
And for the record, I still think `boost::core::is_same` was a bad idea. I would like it to be removed or hidden in `detail`. You want `is_same` - use the one from TypeTraits, there should be none in Core (none public, anyway).  
  
I didn't know we added `enable_if_` in TypeTraits. This, again, perpetuates the mess. We should deprecate one (apparently, the one in Core at this point) and guide everyone to TypeTraits. I would also like us to rename it to the standard `enable_if`.

---

## Comment 12

> Username: pdimov  
> Created at: 2022-10-04 22:26:16 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267653269  

Yeah, the only reason we have `is_same` in Core is because it's needed by lwtest, and Core was/is not allowed to depend on TypeTraits, so I had to duplicate `is_same` here.

---

## Comment 13

> Username: Lastique  
> Created at: 2022-10-04 22:51:45 UTC  
> Updated at: 2022-10-04 22:52:12 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267689683  

Can it be moved to `detail`? Both namespace and directory.

---

## Comment 14

> Username: pdimov  
> Created at: 2022-10-04 22:53:42 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267690972  

Yes in principle, but it will break a lot of stuff now.

---

## Comment 15

> Username: Lastique  
> Created at: 2022-10-04 22:57:06 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1267693501  

We can mark it as deprecated, with warnings. Then remove the deprecated name and header after a couple Boost releases.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2023-02-03 16:48:45 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416130010  

Here is a metric. C++11 libraries should largely not have to include Boost.TypeTraits. I have a handful of C++11 libraries that only require `void_t`.

---

## Comment 17

> Username: Lastique  
> Created at: 2023-02-03 17:45:21 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416198888  

C++ version is irrelevant. You include whatever library you need. If you need something from TypeTraits that is not present in the standard library, you include TypeTraits. The fact that the thing you need is a one-liner is not a ground to pull that one-liner to Core.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2023-02-03 18:50:36 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416266322  

My point is that the chances of needing something from TypeTraits other than `void_t` goes down dramatically in C++11 and later.

---

## Comment 19

> Username: pdimov  
> Created at: 2023-02-03 19:11:21 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416287515  

If you don't need `remove_cvref_t` are you writing code at all? There's also `is_detected` and friends. Also, all the `_t` things are C++14 in `<type_traits>`.

---

## Comment 20

> Username: vinniefalco  
> Created at: 2023-02-03 19:12:07 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416288296  

I have a close relationship with `typename`

---

## Comment 21

> Username: glenfe  
> Created at: 2023-02-03 23:46:23 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416535726  

By the time we add several hundred traits to Core for this reason, people will complain that Core is too heavy.

---

## Comment 22

> Username: Lastique  
> Created at: 2023-02-04 00:15:35 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1416550009  

@pdimov Can we close this? I've deprecated `is_same`, and we (Boost.Core maintainers) seem to agree we don't want to duplicate TypeTraits in Core.

---

## Comment 23

> Username: HDembinski  
> Created at: 2023-02-06 11:59:10 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1418966554  

What an unreasonable discussion. You went from my request of adding void_t, a one-liner to "duplicate TypeTraits in Core" and "add several hundred traits"?  This is not a slippery slope that you open up here.

---

## Comment 24

> Username: glenfe  
> Created at: 2023-02-06 12:39:52 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1419018594  

Aside from `enable_if` (now that we're deprecating `is_same`, and now that people are switching to Core's `allocator_traits`) is there anything in Core that is a duplicate of another facility in Boost?

---

## Comment 25

> Username: vinniefalco  
> Created at: 2023-02-06 12:48:38 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1419028582  

On the flip side, it is easy enough for me to just add this to `<boost/buffers/detail/config.hpp>`:  
```  
// avoid all of Boost.Types for just this  
template<class...> struct make_void { typedef void type; };  
template<class... Ts> using void_t = typename make_void<Ts...>::type;  
```  
  
its just 3 lines

---

## Comment 26

> Username: Lastique  
> Created at: 2023-02-06 12:52:21 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1419032931  

> Aside from `enable_if` (now that we're deprecating `is_same`, and now that people are switching to Core's `allocator_traits`) is there anything in Core that is a duplicate of another facility in Boost?  
  
- `alignof.hpp` vs. TypeTraits' `alignment_of`. Why?  
- `max_align.hpp` vs. TypeTraits' definition of `boost::detail::max_align` in `type_with_alignment.hpp`. The latter could probably switch to Core.  
- `pointer_traits.hpp` vs. Intrusive's `pointer_traits.hpp`. The latter could probably switch to Core.

---

## Comment 27

> Username: glenfe  
> Created at: 2023-02-06 14:40:58 UTC  
> Url: https://github.com/boostorg/core/issues/124#issuecomment-1419192349  

> Why?  
  
I was going to declare that we only moved existing components into Core, not duplicated them here, but I wasn't sure.
