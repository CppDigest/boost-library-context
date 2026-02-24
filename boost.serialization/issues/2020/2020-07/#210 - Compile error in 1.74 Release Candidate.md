# #210 - Compile error in 1.74 Release Candidate [Closed]

> Username: thebrandre  
> Created at: 2020-07-14 07:03:39 UTC  
> Updated at: 2020-08-05 16:06:39 UTC  
> Closed at: 2020-07-15 23:00:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/210  

When using the header `serialization\optional.hpp` with Visual Studio 2017, I'll get a compiler error:  
```  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2988: unrecognizable template declaration/definition  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2143: syntax error: missing ';' before '<'  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2059: syntax error: '<'  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2065: 'T': undeclared identifier  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2923: 'boost::optional': 'T' is not a valid template type argument for parameter 'T'  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2143: syntax error: missing ';' before '{'  
d:\lib\boost_1921ec4c\boost\serialization\optional.hpp(98): error C2447: '{': missing function header (old-style formal list?)  
```  
The simple solution:  
Put a `#include <boost/serialization/version.hpp>` before `#include <boost/serialization/optional.hpp>`  -- I guess this should be included directly in `optional.hpp`?

---

## Comment 1

> Username: robertramey  
> Created at: 2020-07-15 19:43:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/210#issuecomment-658969685  

test_optional passes.  What is the program you are compiling to produce this error?

---

## Comment 2

> Username: robertramey  
> Created at: 2020-07-15 23:00:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/210#issuecomment-659057523  

anyway - I think I've fixed this.  On the develop branch.  Should appear in next release.

---

## Comment 3

> Username: thebrandre  
> Created at: 2020-07-16 06:21:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/210#issuecomment-659185411  

Glad to hear that! Thanks!  
To answer your question: I have compiled our internal code base (which is currently on boost-1.73 and uses boost.serialization a lot) with the release candidate. That's why I couldn't post the code snippts directly.

---

## Comment 4

> Username: thebrandre  
> Created at: 2020-08-05 14:57:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/210#issuecomment-669242627  

The issue has been fixed in commit ["clean up by removing unused headers"](https://github.com/boostorg/serialization/commit/8acf32935bd76d4013902e4841d33b7ffd62c5e5) on develop but it has not been merged into master for 1.74.   
  
Is it still possible to merge that commit?

---

## Comment 5

> Username: robertramey  
> Created at: 2020-08-05 16:06:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/210#issuecomment-669283504  

Hmmm - I don't know.  I don't know the current state of the release.  I had supposed it would have been released by now.  I have come to believe (through very bitter experience) that merging in any changes just before a release is a very bad idea.  So my policy would be to merge this in as soon as 1.74 is released.  This would mean that the changes have the whole development cycle to be tested.  It means I can merge at "my leisure" and not be stuck in a situation where I'm no the hook for breaking the boost release - trust me: you don't want to be there.  
  
I think the best thing would be for you to lend you voice to those who want to keep the release cycle short by not merging in stuff at the last minute.
