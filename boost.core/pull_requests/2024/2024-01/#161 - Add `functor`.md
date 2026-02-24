# #161 Add `functor` [Merged]

> Username: Lastique  
> Created at: 2024-01-23 10:44:41 UTC  
> Updated at: 2024-01-27 16:34:26 UTC  
> Merged at: 2024-01-27 16:34:21 UTC  
> Closed at: 2024-01-27 16:34:21 UTC  
> Url: https://github.com/boostorg/core/pull/161  

This PR:  
  
- Removes C++03 from CI  
- Adds `boost/core/functor.hpp` with `functor` class template, as well as associated tests and docs.  
  
`functor` is a C++17 utility class template that wraps a raw function into a function object class. The raw function is specified in a non-type template parameter, and hence its pointer is not stored internally as a data member (i.e. the `functor` class is empty). `functor::operator()` forwards any arguments to the wrapped raw function and returns its result.  
  
The primary use case is converting raw deleter functions into function object classes for `std::unique_ptr`, `unique_resource` and similar. However, it may also be useful with `std::shared_ptr` and `std::bind` and similar utilities to improve efficiency (compared to using a pointer to function) and/or code size (compared to using a lambda).

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-01-23 10:47:58 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1905777660  

Re. posix-cmake-test on Mac OS, those seem to time out randomly and not caused by this change. I think db20a49e480aa43a7c65cfa79d9b9d55053924f6 was a red herring, since when these jobs succeed, they do in less than 5 minutes, so something about these jobs seems to be wrong. They timeout after the compilation is complete, so I'm not sure what's going on there.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-01-23 15:27:54 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906289002  

I'm sorry, why have you removed C++03 from CI?

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-01-23 15:56:35 UTC  
> Updated_at: 2024-01-23 16:08:35 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906370344  

Because it was failing to build because Boost.Bind dropped it. See [here](https://github.com/boostorg/core/actions/runs/7618885187/job/20750921463#step:8:306), for example. And Boost.SmartPtr [warns](https://github.com/boostorg/core/actions/runs/7618885187/job/20750921463#step:8:271) that it will remove it soon. As well as [Boost.Optional](https://github.com/boostorg/core/actions/runs/7618885187/job/20750921463#step:8:336) and [Boost.Function](https://github.com/boostorg/core/actions/runs/7618885187/job/20750921463#step:8:343). Those libraries are pulled indirectly through Boost.Serialization.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-01-23 17:31:41 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906574493  

Should be fixed.

---

## Comment 5

> Username: Lastique  
> Created_at: 2024-01-23 17:32:53 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906579403  

You mean I should remove the "drop C++03" commit from this PR?

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-01-23 17:33:20 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906580623  

Yeah, CMake on macOS started randomly hanging at some point. I think it was when they updated CMake to the latest version, but it might be something else. No idea what causes it or how it can be fixed.

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-01-23 17:33:42 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906581225  

> You mean I should remove the "drop C++03" commit from this PR?  
  
Yes.

---

## Comment 8

> Username: Lastique  
> Created_at: 2024-01-23 17:35:44 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1906584249  

> > You mean I should remove the "drop C++03" commit from this PR?  
>   
> Yes.  
  
Done.

---

## Comment 9

> Username: Lastique  
> Created_at: 2024-01-26 08:46:02 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1911682243  

Are there any comments or objections to merging this?

---

## Comment 10

> Username: pdimov  
> Created_at: 2024-01-26 17:12:56 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1912401641  

It's a good component, although there's always the question of putting more things into Core.  
  
I think that you should document and test that `functor` is callable with some arguments if and only if `Function` is callable with them, and change the implementation to do that. At the moment I think that neither `decltype(auto)` nor `noexcept` have the effect of SFINAE-ing properly; it's better to use `decltype(expr)` as the return type.  
  
The Returns and Throws clauses should probably be replaced with `Effects: return ...;`

---

## Comment 11

> Username: Lastique  
> Created_at: 2024-01-26 17:23:54 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1912415278  

I can SFINAE-enable the `operator()` but is there a case where that would be useful, other than type traits? Function objects don't participate in overload resolution.  
  
> The Returns and Throws clauses should probably be replaced with `Effects: return ...;`  
  
Yes, although I would keep "Throws:" since the synopsis shows `noexcept(...)` for brevity, implying that the `noexcept`-ness will be documented in the `operator()` documentation.

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-01-26 17:33:20 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1912427321  

I can't give you an example right now, but a lot of places tend to use this type trait.

---

## Comment 13

> Username: Lastique  
> Created_at: 2024-01-26 19:00:29 UTC  
> Url: https://github.com/boostorg/core/pull/161#issuecomment-1912545295  

> I think that you should document and test that `functor` is callable with some arguments if and only if `Function` is callable with them, and change the implementation to do that. At the moment I think that neither `decltype(auto)` nor `noexcept` have the effect of SFINAE-ing properly; it's better to use `decltype(expr)` as the return type.  
>   
> The Returns and Throws clauses should probably be replaced with `Effects: return ...;`  
  
Done.

---
