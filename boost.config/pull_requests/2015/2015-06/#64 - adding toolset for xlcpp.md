# #64 adding toolset for xlcpp [Merged]

> Username: camorton2  
> Created at: 2015-06-02 18:02:26 UTC  
> Updated at: 2015-07-22 14:37:42 UTC  
> Merged at: 2015-07-19 16:43:02 UTC  
> Closed at: 2015-07-19 16:43:02 UTC  
> Url: https://github.com/boostorg/config/pull/64  

adding a toolset 'xlcpp' for the new IBM compilers based on clang, old toolset 'vacpp' will remain for older IBM compilers not based on clang

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-06-02 18:36:30 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-108047664  

What's the difference between this and the regular clang.hpp?  Is there any reason why that one can't be used as is?  
  
Either way the selection logic in select_compiler.hpp will need changing.

---

## Comment 2

> Username: camorton2  
> Created_at: 2015-06-02 18:41:42 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-108049537  

There's no difference other than the name of the compiler.  
So I can select clang.hpp with the xlcpp toolset in select_compiler.hpp ?

---

## Comment 3

> Username: camorton2  
> Created_at: 2015-06-02 18:43:21 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-108050104  

I created a new file because its likely we might need to change something as we continue testing - we don't have all the clang features supported.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-06-02 18:53:34 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-108053773  

On 02/06/2015 19:43, Catherine Morton wrote:  
  
> I created a new file because its likely we might need to change   
> something as we continue testing - we don't have all the clang   
> features supported.  
  
Understood, but as most of that file just uses has_feature etc, should   
anything unsupported return false and do the right thing? Seems like   
it's worth a try at least?  But whatever, I'll go with whatever works   
best.  The main danger with a separate file is that it gets out of date   
/ under-maintained I guess.

---

## Comment 5

> Username: camorton2  
> Created_at: 2015-06-02 18:59:43 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-108056748  

I made the changes to include/boost/config/select_compiler_config.hpp

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-06-03 18:10:09 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-108552059  

Merged to _develop_.  Closing manually.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2015-06-20 07:34:43 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-113720987  

This isn't quite working:  
  
Looking at: http://www.boost.org/development/tests/develop/developer/output/IBM%20XL%20C++%20Compiler%20For%20Linux%2C%20V13-1-1-boost-bin-v2-libs-config-test-config_info-test-vacpp-release.html  I see that clang.hpp is still being selected.  
  
The compiler also appears to be defining **SIZEOF_INT128** without actually supporting __int128 which causes several sets of tests to fail, for example: http://www.boost.org/development/tests/develop/developer/output/IBM%20XL%20C++%20Compiler%20For%20Linux%2C%20V13-1-1-boost-bin-v2-libs-config-test-config_test-test-vacpp-release.html.  I guess we can just unconditionally disable the code that activates __int128 support once the correct config file is selected.  Do you have a fix that will cause the correct one to be selected?

---

## Comment 8

> Username: camorton2  
> Created_at: 2015-06-20 10:37:01 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-113743532  

Those tests were still using the vacpp toolset, we haven't enabled the xlcpp toolset yet

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2015-06-20 11:07:58 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-113746473  

I'm not sure I follow - if you're testing Git-develop then the changes in this PR should be in effect?

---

## Comment 10

> Username: camorton2  
> Created_at: 2015-06-20 11:30:57 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-113748529  

Its a chicken/egg thing.   I created 3 pull requests to get the new toolset configured, but I can't enable it on our regular run until they are all in place.... Unless I misunderstand, the run script for the tests downloads a fresh copy of the source so it won't have all of my pull requests.   Our plan was to keep running with the previous toolset until all the pull requests are closed, then switch over and continue working out problems as we investigate failures.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2015-06-20 16:04:12 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-113786641  

> Its a chicken/egg thing. I created 3 pull requests to get the new   
> toolset configured, but I can't enable it on our regular run until   
> they are all in place.... Unless I misunderstand, the run script for   
> the tests downloads a fresh copy of the source so it won't have all of   
> my pull requests. Our plan was to keep running with the previous   
> toolset until all the pull requests are closed, then switch over and   
> continue working out problems as we investigate failures.  
>   
> I suspect we may be talking at cross-purposes, but it shouldn't matter   
> at all which Boost.Build toolset is used (if that's the other PR),   
> especially as most users won't be using Boost.Build when they _use_   
> Boost.  Wait, compiler pre-defines _both_ **clang** and **ibmxl** yes?    
> If that's the case then **clang** is seen/configured first in   
> select_compiler_config.hpp and so your patch has no effect?   If that's   
> the case let me know and I'll make the "obvious" fix.  Thanks, John.  
  
PS defining **SIZEOF_INT128** without actually supporting __int128 is   
still a compiler bug IMO ;)

---

## Comment 12

> Username: camorton2  
> Created_at: 2015-06-21 00:46:13 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-113845597  

Thanks for pointing that out, please fix it for me.  
  
I agree about the compiler bug having **SIZEOF_INT128**  defined without actually supporting __int128 - I've reported the defect.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2015-06-23 17:49:56 UTC  
> Url: https://github.com/boostorg/config/pull/64#issuecomment-114589026  

Tentatively fixed in https://github.com/boostorg/config/commit/046b9d95e0d359f8f63eb6216e1e8345ecca0569, need to let the tests cycle to see.

---
