# #100 Most members of std::allocate are deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2018-01-02 15:16:41 UTC  
> Updated at: 2018-10-14 07:46:03 UTC  
> Merged at: 2018-10-14 07:45:13 UTC  
> Closed at: 2018-10-14 07:45:13 UTC  
> Url: https://github.com/boostorg/graph/pull/100  

Replace them by their cousins from std::allocator_traits. Without that, heaps of deprecation warnings will fall onto humble users when compiling with MSVC 15 in C++17 mode.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:40:55 UTC  
> Url: https://github.com/boostorg/graph/pull/100#issuecomment-417739200  

I'm helping out with the PR backlog. Looks like you have a code change.  I don't like the preprocessor order and would rather have it use built in C++ standard features before Boost's.  Could you revise your PR to comply?  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: DanielaE  
> Created_at: 2018-08-31 17:47:06 UTC  
> Url: https://github.com/boostorg/graph/pull/100#issuecomment-417740862  

It seems like you haven't fully grasped the PR. There are no Boost features involved at all - it's all about using C++ allocators from the C++11 era (where possible) rather than the obsolete stuff.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-08-31 18:38:22 UTC  
> Url: https://github.com/boostorg/graph/pull/100#issuecomment-417754926  

Let me take another look here.  There is quite the back log I'm looking through.

---

## Comment 4

> Username: anadon  
> Created_at: 2018-08-31 18:43:01 UTC  
> Url: https://github.com/boostorg/graph/pull/100#issuecomment-417756224  

I'm going to have to dig into this more.  This will be handled after the first two batches of merges.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2018-10-12 17:09:29 UTC  
> Url: https://github.com/boostorg/graph/pull/100#issuecomment-429395485  

I haven't checked this through, but we do need a fix for C++17 - I'm going to close and re-open in the hopes of triggering a CI build.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-10-14 07:46:03 UTC  
> Url: https://github.com/boostorg/graph/pull/100#issuecomment-429604161  

Merged, should all be uncontroversial changes to bring things in line with C++17.

---
