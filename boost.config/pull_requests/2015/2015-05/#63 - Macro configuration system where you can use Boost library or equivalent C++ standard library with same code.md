# #63 Macro configuration system where you can use Boost library or equivalent C++ standard library with same code [Closed]

> Username: eldiener  
> Created at: 2015-05-28 18:53:41 UTC  
> Updated at: 2015-07-10 08:09:52 UTC  
> Closed at: 2015-07-10 04:29:13 UTC  
> Url: https://github.com/boostorg/config/pull/63  

I wrote tests for these macros for each library mentioned as .ipp files. Then I realized that these tests need to be added as regular tests for the appropriate Boost libraries themselves as the tests in Boost.config would produce dependencies on these other Boost libraries, which of course we do not want. If my set of macros is added to Boost.config I will create PRs for all the libraries mentioned with the appropriate test to exercise this added Boost.config set of macros.  
  
The macros added here are not automatically brought in when   
  
```  
#include <boost/config.h>  
```  
  
is used, as my documentation for them explains.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-06-04 20:57:23 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-109048486  

I have written tests for each of the Boost libraries handled by these macros, and tested the usage of these macros successfully in those tests. I will be adding pull requests for each of those libraries as soon as this pull request is pushed to 'develop' of config. If there are no objections I am going to wait another day and then push this change to config on the 'develop' branch, as well as announce on the Boost developers mailing list what this change is about. Since this change does not affect normal use of Boost.config in any way, pushing this change to 'develop' will not affect anyone unless they make use of the macros entailed. The change is explained fully in the documentation update of this PR.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-06-05 18:46:13 UTC  
> Updated_at: 2015-06-05 19:11:38 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-109394940  

I think we're using CXX as the abbreviation for C++, not CPP?  
  
Also, we typically spell the library name starting with a capital letter (i.e. Boost.Config instead of Boost.config). Sorry for nitpicking.

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-06-05 19:16:38 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-109409101  

Where are we using CXX for C++ ? I know config uses CXX11 for C++11 but that doesn't mean that CXX is always C++. Nonetheless I can easily change it if there is a consensus to do so. But I really chose CPP, for one reason, so as to avoid any cash with macros that might start with BOOST_CXX, which I thought might be more common.

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-06-05 19:21:28 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-109411228  

Yes, I was talking about CXX11 and CXX14. If this is going to be part of Boost.Config, I think it is logical follow its existing naming practice. I don't think any library but Boost.Config is going to use BOOST_CXX.  
  
OTOH, these headers could be a separate library. Then a different, more distinct prefix could be chosen.

---

## Comment 5

> Username: eldiener  
> Created_at: 2015-06-05 20:29:34 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-109431267  

I don't want the macros to refer to C++11 or C++14 as such. The macros are meant to work with generically with Boost libraries which become part of the C++ standard libraries. It does not matter whether those libraries were added during C++03, C++11, C++14, or an upcoming C++17. So I would rather leave out anything about them that refers to a given C++ standard release.  
  
I am not against using CXX instead of CPP to refer to C++ in general as part of the macro's prefix. That change is trivial to make. But let me wait until any discussion of this PR is more or less finished on the Boost developers mailing list to see what others may say.  
  
I would like to avoid this PR as a separate library. I strongly believe it is a "configuration", its based on other Boost.config macros, and as a separate library it would be very thin on overall functionality. I view it thusly: the macros are a way to "configure" Boost so that it uses either the appropriate Boost library or its equivalent C++ standard library in a generic way. As such it is part of the Boost.config agenda.

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-07-10 04:29:13 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-120222115  

I am going to develop my PR as a separate library, as there are expansions to the functionality I want to do and the expansions make the PR as an addition to config unwieldy. Developed as a separate library i believe it will have more chance to garner interest among Boost developers.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2015-07-10 08:09:52 UTC  
> Url: https://github.com/boostorg/config/pull/63#issuecomment-120282564  

> I am going to develop my PR as a separate library, as there are   
> expansions to the functionality I want to do and the expansions make   
> the PR as an addition to config unwieldy. Developed as a separate   
> library i believe it will have more chance to garner interest among   
> Boost developers.  
>   
> OK thanks, I was wondering what to do with this PR.  
  
Note that even if initially developed as a separate entity, that doesn't   
preclude it ultimately ending up inside Config if that's the right place   
for it.  
  
However, getting some more feedback than is easily possible for a PR is   
probably best at this stage.  
  
Regards, John.

---
