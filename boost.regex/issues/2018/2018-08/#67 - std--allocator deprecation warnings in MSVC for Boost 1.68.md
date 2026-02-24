# #67 - std::allocator deprecation warnings in MSVC for Boost 1.68 [Closed]

> Username: rcdailey  
> Created at: 2018-08-20 14:43:41 UTC  
> Updated at: 2018-11-11 16:51:36 UTC  
> Closed at: 2018-11-11 16:51:36 UTC  
> Url: https://github.com/boostorg/regex/issues/67  

Using MSVC 15.8, I get the following warning using boost regex:  
  
> 2>e:\code\frontend2\source\core\thirdparty\boost\include\boost\regex\v4\match_results.hpp(60): warning C4996: 'std::allocator<_Ty>::const_reference': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
2>        with  
2>        [  
2>            _Ty=boost::sub_match<const char *>  
2>        ] (compiling source file E:\code\frontend2\source\Core\MsrInterface\Source\Msr\Parsing\MagstripeParser.cpp)  
  
I have C++17 enabled. Are these supposed to be fixed in v1.68? Or am I missing something? I can disable the warnings but I'd rather have some proper fixes in Boost.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-20 14:56:49 UTC  
> Url: https://github.com/boostorg/regex/issues/67#issuecomment-414346096  

The patches are in develop, but apparently didn't get merged to master.  My bad.  Will take care of it fr the next release.

---

## Comment 2

> Username: tobias-loew  
> Created at: 2018-10-30 08:15:19 UTC  
> Updated at: 2018-10-30 08:17:12 UTC  
> Url: https://github.com/boostorg/regex/issues/67#issuecomment-434209367  

Just a remark.  
While removing std::iterator from boost/ublas I stumbled over the following issue:  
Removing std::iterator may lead to compilation errors since the derived iterator no longer has an "std" base class, thus ADL won't search the std-namespace for unqualified calls.  
I searched the stl for empty classes to put as base and the best types I could find are  
- std::monospace : empty type,  in  < variant >  
- std::pair<> : not completely empty,  in < tuple >  
  
I couldn't find any class designed as the "hoist into the std-namespace" class.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-11 14:47:50 UTC  
> Url: https://github.com/boostorg/regex/issues/67#issuecomment-437676002  

@jzmaddock was the fix for this merged into master?  If so the issue can be resolved.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-11-11 16:51:36 UTC  
> Url: https://github.com/boostorg/regex/issues/67#issuecomment-437685723  

Yes merged now.
