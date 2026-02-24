# #5 Fix ptr_list compile break [Closed]

> Username: MarcelRaad  
> Created at: 2015-03-02 17:40:29 UTC  
> Updated at: 2017-11-07 13:01:51 UTC  
> Closed at: 2017-11-05 16:14:50 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5  

https://github.com/boostorg/utility/commit/651a869d4f9479dd3dfdd0293305a60b8c8d0e1c broke the ptr_list regression tests as boost::next now uses operator+= if available instead of std::advance, which doesn't work for list iterators.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-07-07 22:22:57 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-119361757  

My first response to this is to fix `boost::next`. Looking at that now.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2015-07-08 09:28:41 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-119518982  

Thanks! That would be even better of course.

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-05 16:14:50 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-341984686  

Looks like this is no longer an issue.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2017-11-05 21:33:03 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342007525  

Yes, I can confirm this has been fixed by https://github.com/boostorg/iterator/commit/d251a6f5154cdda5bb1622923220e7b3428bebf5.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-11-05 22:13:26 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342010187  

Marcel, do you use `ptr_container`? There are many unmerged changes in develop and someone who cares about the library and is familiar with it needs to go through them, review them and determine what needs to be merged.

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-11-06 02:15:38 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342028895  

Never mind, I decided to just go ahead and merge.

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2017-11-06 09:03:41 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342086001  

Yes, I've been using the develop branch integrated into official Boost releases for a long time on older compilers (MSVC 9 SP1, GCC 4.2.1, clang C++03) without any problems. Thanks!

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-11-06 12:15:37 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342131571  

Thanks for the confirmation. :-)

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-11-07 12:50:18 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342472587  

Are there other Boost libraries for which you use the develop branch and would like it merged?

---

## Comment 10

> Username: MarcelRaad  
> Created_at: 2017-11-07 13:01:51 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/5#issuecomment-342475342  

@pdimov Yes, tuple, thanks for asking. The released version has an implicit one-element constructor, which regularly led to bugs in code I'm working with.

---
