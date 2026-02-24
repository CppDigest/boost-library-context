# #9 Most members of std::allocate are deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2017-12-29 10:49:13 UTC  
> Updated at: 2018-01-14 07:50:29 UTC  
> Merged at: 2017-12-29 12:45:57 UTC  
> Closed at: 2017-12-29 12:45:57 UTC  
> Url: https://github.com/boostorg/heap/pull/9  

Replace them by their cousins from std::allocator_traits.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: timblechmann  
> Created_at: 2017-12-29 11:14:58 UTC  
> Url: https://github.com/boostorg/heap/pull/9#issuecomment-354432970  

thanks for the patch! the patch is quite intrusive so i wonder, if it would be reasonable to introduce a compatibility layer, similar to https://github.com/boostorg/lockfree/pull/39/files#diff-5cc965699b3eafbfdc551e045e0d20b5

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-12-29 11:42:22 UTC  
> Url: https://github.com/boostorg/heap/pull/9#issuecomment-354435687  

Well, I've seen the mentioned compatibility layer, and honestly, I don't like it: it goes only a fraction of the path to the end (see my additional patch on top of that). You need full conformance to modern allocators to open the road to the future (e.g. C++17's polymorphic allocators). Another compatibility layer looks pretty much like std::allocator_traits - implemented in terms of std::allocator_traits.

---

## Comment 3

> Username: timblechmann  
> Created_at: 2017-12-29 12:45:44 UTC  
> Url: https://github.com/boostorg/heap/pull/9#issuecomment-354441715  

yeah, i'm not totally sure what's the best way to handle deprecations, especially as long as we're still supporting c++98. lots of code could be done much more elegant without backwards compatibility :/

---
