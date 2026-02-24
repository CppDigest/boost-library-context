# #767 Fix missing includes in `match_manip.hpp` [Merged]

> Username: Lastique  
> Created at: 2023-09-02 20:48:12 UTC  
> Updated at: 2023-10-08 23:35:54 UTC  
> Merged at: 2023-10-08 23:08:19 UTC  
> Closed at: 2023-10-08 23:08:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/767  

Use `failbit` as a dependent name to avoid having to include `<ios>` for `std::ios_base::failbit`.  
  
Add missing includes for `enable_if` and `mpl::true_`/`mpl::false_`.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2023-10-07 16:50:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/767#issuecomment-1751767945  

This misses a couple of other uses of `std::ios_base::failbit`, e.g. in `include/boost/spirit/home/karma/stream/format_manip.hpp` & `include/boost/spirit/home/karma/stream/detail/format_manip.hpp`, see https://github.com/Flamefire/spirit/actions/runs/6442261776/job/17492909668 & https://github.com/boostorg/spirit/pull/774

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-10-08 01:00:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/767#issuecomment-1751880758  

I've added a few more workarounds, but it looks like missing includes are all over Boost.Spirit.  
  
This PR is not meant to be an exhaustive fix for this problem, just something I found during the switch to `invoke_swap`.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2023-10-08 09:28:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/767#issuecomment-1751975544  

Interesting that you seem to have required more changes, I added [much less ](https://github.com/boostorg/spirit/pull/774/commits/b7e03f7e4c407e37b1ee0d44de34e7fc6c1e9177) to make the [test PR](https://github.com/boostorg/spirit/pull/774/) pass  
  
Anyway it's a good way forward.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-10-08 10:46:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/767#issuecomment-1751995227  

On October 8, 2023 12:29:09 PM Alexander Grund ***@***.***>   
wrote:  
  
> Interesting that you seem to have required more changes, I added [much less   
> ](https://github.com/boostorg/spirit/pull/774/commits/b7e03f7e4c407e37b1ee0d44de34e7fc6c1e9177)   
> to make the [test PR](https://github.com/boostorg/spirit/pull/774/) pass  
  
My original set of changes were enough to get the tests passing for me. The   
added ones are what you pointed out and what caught my eye while I was   
fixing those.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2023-10-08 15:41:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/767#issuecomment-1752089021  

> My original set of changes were enough to get the tests passing for me. The added ones are what you pointed out and what caught my eye while I was fixing those.  
  
I see makes sense. I tested your changes in #774 and it passes CI so this is good to merge :+1:

---
