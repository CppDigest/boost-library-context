# #52 Fix C++17 deprecation warning from std::allocator::is_always_equal [Closed]

> Username: edsavage  
> Created at: 2021-12-01 15:28:50 UTC  
> Updated at: 2021-12-08 16:15:58 UTC  
> Closed at: 2021-12-08 16:15:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/52  

When compiling with support for C++17 the following deprecation warning is encountered  
```  
C:\usr\local\include\boost-1_77\boost/unordered/detail/implementation.hpp(1452,7): warning C4996: 'std::allocator<std::pair<const std::basic_string<char,std::char_traits<char>,std::allocator<char>>,std::basic_string<char,std::char_traits<char>,std::allocator<char>>>>::is_always_equal': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
```  
  
Following the lead suggested here  
https://groups.google.com/g/boost-developers-archive/c/3MOWuQgEEy4/m/boIwsJsdBAAJ  
and using `boost::allocator_is_always_equal` from  
`<boost/core/allocator_access.hpp>` does fix the source of the deprecation warning.

---

## Comment 1

> Username: glenfe  
> Created_at: 2021-12-01 22:53:40 UTC  
> Url: https://github.com/boostorg/unordered/pull/52#issuecomment-984131222  

All of the Unordered allocator traits machinery should be removed and replaced with `<boost/core/allocator_access.hpp>` which simplifies Unordered's implementation drastically. @LeonineKing1199 is working on this.

---

## Comment 2

> Username: glenfe  
> Created_at: 2021-12-08 14:36:09 UTC  
> Url: https://github.com/boostorg/unordered/pull/52#issuecomment-988868862  

@LeonineKing1199  can you confirm that this is now fixed in `develop`?

---

## Comment 3

> Username: cmazakas  
> Created_at: 2021-12-08 15:34:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/52#issuecomment-988919865  

@glenfe @edsavage   
  
I can happily confirm that the deprecation warning no longer appears!  
  
For example, consider the logs from AppVeyor [here](https://ci.appveyor.com/project/danieljames/unordered-qtwe6/builds/41791106/job/8g9bjxf86n605l5a) which is a slightly older build.  
  
Compare it to the logs in the [latest AppVeyor run](https://ci.appveyor.com/project/danieljames/unordered-qtwe6/builds/41808265/job/75q15g26mv3u9e1h) here.  
  
No more warnings!  
  
If you wish to confirm yourself, I'd recommend downloading the logs themselves as the AppVeyor site seems to struggle with rendering that much output whereas downloading the logs and opening them with a minimal text editor is quite fast.

---

## Comment 4

> Username: edsavage  
> Created_at: 2021-12-08 16:15:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/52#issuecomment-988958326  

Closing this PR as this the issue it was trying to fix has been addressed in a better way.

---
