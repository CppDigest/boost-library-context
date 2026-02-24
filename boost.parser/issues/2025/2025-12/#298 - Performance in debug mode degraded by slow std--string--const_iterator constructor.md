# #298 - Performance in debug mode degraded by slow std::string::const_iterator constructor [Open]

> Username: andreasbuhr  
> Created at: 2025-12-02 17:08:21 UTC  
> Updated at: 2025-12-10 17:17:15 UTC  
> Url: https://github.com/boostorg/parser/issues/298  

Unfortunately, boost.parser is very slow in debug mode.  
Fortunately, this would be rather easy to change.  
  
I have colleagues complaining because the Boost.Parser based parser takes forever in debug mode. I fired up VTune and quickly found that more than 60% of the total runtime is spent in the constructor and destructor of std::string::const_iterator. Boost.Parser is written under the assumption (it seems) that copying an iterator is basically free, which is probably true in release mode. But in MSVC/Debug it is a very expensive operation.  
  
Looking at the code, I got the impression that in most places, nothing would be lost passing the iterators as const-ref instead of by-value. And the debug runtime would dramatically improve. Would you be open to change to const-ref for most iterator arguments? If so, I'd consider creating a Pull request doing this. Or am I missing a use case where the pass-by-value is beneficial?

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-12-06 05:57:13 UTC  
> Url: https://github.com/boostorg/parser/issues/298#issuecomment-3619605035  

There are two reasons for pass-by-value with iterators.  
  
1) In C++20 and later, you may be passing an input iterator, and those are (again, as of C++20, sometimes move-only).  I'm only mentioning this for completeness; I don't really care about the input iterator case, because they don't work with Boost.Parser for other reasons (like, backtracking does not work).  
  
2) Passing by reference can be a pessimization in release builds, but only in pretty infrequent cases.  A `std::string::const_iterator const &` is a pointer-to-pointer; you have two dependent loads to access its pointee instead of the one you would have for just `std::string::const_iterator`.  That noted, all the Boost.Parser code is inline.  So you only really suffer from this effect when inlining pressure/inlining recursion depth is high enough that the inliner stops inlining some of these functions and you have to actually process the `const &` part instead of just inlining it away.  So: if you generate a PR, and can show that there is no (or only minor) release-build perf regression, I'll merge it.  I don't need a dissertation. :)  Just run some basic end-to end runs of something hairier than the simple tests.

---

## Comment 2

> Username: andreasbuhr  
> Created at: 2025-12-10 17:17:15 UTC  
> Url: https://github.com/boostorg/parser/issues/298#issuecomment-3638140686  

I started working on it in https://github.com/boostorg/parser/pull/300.
