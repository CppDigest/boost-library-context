# #1272 - MSVC 2017: std::allocator<void> is deprecated in C++17 [Closed]

> Username: haquocviet  
> Created at: 2018-10-19 09:06:14 UTC  
> Updated at: 2018-11-28 00:44:54 UTC  
> Closed at: 2018-11-28 00:44:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1272  

Hi,  
I build a program using your library and I got so many warnings like below:  
  
`5>  e:\vcpkg\installed\x64-windows\include\boost\beast\http\impl\write.ipp(360): warning C4996: 'std::allocator<void>': warning STL4009: std::allocator<void> is deprecated in C++17. You can define _SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning. `  
  
Of course, those warning shall be disappeared if I define suggested things. This is not a bug, however, I think you should consider any better replacement of std::allocator<void> to conform to C++17.  
  
MSVC 2017 15.8.6 with /std:c++17 enabled  
BOOST_BEAST_VERSION 181

---

## Comment 1

> Username: eido79  
> Created at: 2018-10-19 18:45:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-431460671  

Isn't this the same of #1225?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-19 19:10:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-431467715  

My advice here would be to define `_SILENCE_CXX17_ALLOCATOR_VOID_DEPRECATION_WARNING`. This warning is bogus; this use of `allocator<void>` is correct.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-10-19 19:12:59 UTC  
> Updated at: 2018-10-19 19:13:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-431468650  

This is incorrect behavior in MSVC, see: https://github.com/chriskohlhoff/asio/issues/290#issuecomment-371867040

---

## Comment 4

> Username: haquocviet  
> Created at: 2018-10-23 07:34:35 UTC  
> Updated at: 2018-10-23 07:35:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-432129865  

I am not sure if MS is aware this confusion but IMHO I think they have a reason to mark std::allocator deprecated.  
Please look at this:  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0174r0#2.4  
  
Anyway currently this warning is not a big deal (at least for me).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-10-23 13:44:07 UTC  
> Updated at: 2018-10-23 13:44:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-432252459  

Networking TS defines a "ProtoAllocator" which is essentially `allocator<void>`. All you can really do with such an allocator is rebind it to get a "real" allocator. I think this is a good change, because it stops forcing authors from having to pick an arbitrary type to parameterize their allocator (e.g. `allocator<char>`) for the case where the actual desired type is an implementation detail or otherwise not public. I agree the warning is irritating.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-10-23 13:58:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-432258280  

Peter is right, the warning is spurious.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-441859429  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-11-28 00:44:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1272#issuecomment-442274906  

I have modified the build scripts to squelch this warning in msvc.
