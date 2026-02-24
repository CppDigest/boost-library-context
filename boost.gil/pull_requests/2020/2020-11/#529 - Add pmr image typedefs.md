# #529 Add pmr image typedefs [Merged]

> Username: sdebionne  
> Created at: 2020-11-09 16:42:45 UTC  
> Updated at: 2022-06-28 07:53:21 UTC  
> Merged at: 2022-06-28 06:35:28 UTC  
> Closed at: 2022-06-28 06:35:28 UTC  
> Url: https://github.com/boostorg/gil/pull/529  

### Description  
when building with C++17 standard, adds pmr (polymorphic memroy resource) image typedefs (e.g. `gil::pmr::gray8_image_t = using image<gray8_pixel_t, false, std::pmr::polymorphic_allocator<unsigned char>`).  
  
Fixes #528.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Conditionally include `<memory_resource>` and typedefs  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-05-04 10:14:04 UTC  
> Updated_at: 2022-06-28 05:31:51 UTC  
> Url: https://github.com/boostorg/gil/pull/529#issuecomment-1117144816  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/529?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#529](https://codecov.io/gh/boostorg/gil/pull/529?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (59703e6) into [develop](https://codecov.io/gh/boostorg/gil/commit/8d7034c71cdb3ea1e946f57ca3b30e81b8a8b854?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (8d7034c) will **increase** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #529   +/-   ##  
========================================  
  Coverage    80.84%   80.84%             
========================================  
  Files          116      116             
  Lines         5121     5127    +6       
========================================  
+ Hits          4140     4145    +5       
- Misses         981      982    +1       
```

---

## Comment 2

> Username: sdebionne  
> Created_at: 2022-05-04 11:56:21 UTC  
> Url: https://github.com/boostorg/gil/pull/529#issuecomment-1117224188  

I would be happy to remove the `#if !defined(BOOST_NO_CXX17_HDR_MEMORY_RESOURCE)` guards if we decide to move to C++17 😁

---

## Review 3 [Commented]

> Username: sdebionne  
> Created_at: 2022-05-20 08:23:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/529#pullrequestreview-979717897  

📁 include/boost/gil/image.hpp

```diff
 242 |+             swap(_alloc, img._alloc);
 243 |+         else
 244 |+             BOOST_ASSERT(_alloc == img._alloc);
```

> Username: sdebionne  
> Created_at: 2022-05-20 08:23:18 UTC  
> Updated_at: 2022-05-20 08:23:19 UTC  
> Url: https://github.com/boostorg/gil/pull/529#discussion_r877875588  

@mloskot If that can help with the review, this is he important bit (that I would like to be merged in 1.80). The rest can be moved to another PR if necessary.

> Username: mloskot  
> Created_at: 2022-05-20 08:36:35 UTC  
> Updated_at: 2022-05-20 08:36:43 UTC  
> Url: https://github.com/boostorg/gil/pull/529#discussion_r877887803  

I'm sorry for the delay, I've missed this one. On it...


---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2022-05-20 09:11:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/529#pullrequestreview-979781661  

LGTM  
  
By the way, see https://github.com/boostorg/gil/discussions/676

---

## Comment 5

> Username: mloskot  
> Created_at: 2022-06-26 11:37:29 UTC  
> Url: https://github.com/boostorg/gil/pull/529#issuecomment-1166504041  

I have updated this PR with latest `develop` and I am going to merge it if no major CI issues are revealed.

---

## Comment 6

> Username: mloskot  
> Created_at: 2022-06-27 21:42:15 UTC  
> Updated_at: 2022-06-27 22:02:39 UTC  
> Url: https://github.com/boostorg/gil/pull/529#issuecomment-1167939251  

The GCC 11 drops the warning:  
  
```  
include/boost/gil/image.hpp:241:12: warning: ‘if constexpr’ only available with ‘-std=c++17’ or ‘-std=gnu++17’  
  241 |         if constexpr (std::allocator_traits<Alloc>::propagate_on_container_swap::value)  
      |            ^~~~~~~~~  
```  
  
and C++14 compilation mode is failing, obviously. Some `#ifdef`-s are missing :)

---

## Comment 7

> Username: sdebionne  
> Created_at: 2022-06-28 07:39:13 UTC  
> Updated_at: 2022-06-28 07:44:12 UTC  
> Url: https://github.com/boostorg/gil/pull/529#issuecomment-1168352081  

Thanks for looking into the issue, `std::allocator_traits` is C++11 but indeed `if constexpr` is C++17... I guess I would need to re-implement that part with SFINAE. But since we are switching to C++17 anyway...

---
