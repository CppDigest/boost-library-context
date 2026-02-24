# #68 Enable BOOST_STL_INTERFACES_USE_DEDUCED_THIS on Clang 19 and MSVC 19.41 [Merged]

> Username: ednolan  
> Created at: 2024-09-06 03:43:14 UTC  
> Updated at: 2024-10-24 22:47:40 UTC  
> Merged at: 2024-10-24 22:47:40 UTC  
> Closed at: 2024-10-24 22:47:40 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/68  

Clang 19 and MSVC 17.11.35222.181 both have support for deducing this that is complete enough to allow the v3 version of this library to work but not complete enough for the compilers to define __cpp_explicit_this_parameter. By adding  
BOOST_STL_INTERFACES_ENABLE_DEDUCED_THIS, users of those compilers can explicitly opt into the v3 interface even when their compilers do not advertise support for deducing this.  
  
BOOST_STL_INTERFACES_ENABLE_CONCEPTS is added for symmetry.

---

## Review 1 [Changes requested]

> Username: tzlaine  
> Created_at: 2024-09-27 23:52:00 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/stl_interfaces/pull/68#pullrequestreview-2334817498  

I don't want to have two different macros one called DO_IT and one called DONT_DO_IT.  What are the intended semantics when they say different things?  
  
Moreover, this is not the way this is done in Boost.  If there is a specific version after which language support is available, then we should check for that specific range or versions.  Could you update this so that it automatically detects the MSVC verions in question with `defined(__cpp_explicit_this_parameter) || YOUR_THING_HERE`?

---

## Comment 2

> Username: tzlaine  
> Created_at: 2024-10-24 01:33:01 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/68#issuecomment-2434025635  

@ednolan Ping.  The deadline approaches. :) Could you make the requested changes?

---

## Comment 3

> Username: ednolan  
> Created_at: 2024-10-24 04:35:43 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/68#issuecomment-2434274089  

I made the requested changes, let me know if the most recent commit is good. I've verified that utf_view CI passes with it on both MSVC and Clang: https://github.com/ednolan/utf_view/actions/runs/11492656840

---

## Comment 4

> Username: tzlaine  
> Created_at: 2024-10-24 22:47:36 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/68#issuecomment-2436471525  

Thanks!  It will be in the next Boost release.

---
