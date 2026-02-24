# #74 - Segfault with Xcode 8.3 using shared library built with Xcode 6.4 [Closed]

> Username: wesm  
> Created at: 2018-12-19 15:49:38 UTC  
> Updated at: 2020-01-22 18:07:21 UTC  
> Closed at: 2020-01-22 18:07:20 UTC  
> Url: https://github.com/boostorg/regex/issues/74  

We're having some issues with boost_regex 1.69.0 that weren't present in version 1.68.0 and prior. As background, we maintain "portable" binary packages for Boost for Linux, macOS, and Windows in conda-forge. These are currently built with Xcode 6.4   
  
https://github.com/conda-forge/boost-cpp-feedstock  
  
When upgrading to 1.69.0 we started getting segfaults in newer versions of Xcode, here's the backtrace we are seeing:  
  
https://gist.github.com/kszucs/abee56cf39c470e0392be2d8453485a4  
  
If we build Boost with the same version of Xcode this problem does not occur.  
  
Reported issue in the packaging repo:  
  
https://github.com/conda-forge/boost-cpp-feedstock/issues/40  
  
This originally showed up as a downstream report in https://issues.apache.org/jira/browse/ARROW-4050  
  
Any ideas what could be causing this or how to fix?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-12-19 18:09:34 UTC  
> Url: https://github.com/boostorg/regex/issues/74#issuecomment-448691440  

>Any ideas what could be causing this or how to fix?  
  
None at present - there have been no changes to cpp_regex_traits.hpp for 3 years, so it should be stable.  
  
What's happening here is that the cpp_regex_traits<>::m_pcollate member is NULL when used, even though it's set in `cpp_regex_traits::imbue()` via std::use_facet and this is called by the constructor.    
Are you able to debug?

---

## Comment 2

> Username: wesm  
> Created at: 2018-12-19 18:46:25 UTC  
> Updated at: 2018-12-19 18:46:31 UTC  
> Url: https://github.com/boostorg/regex/issues/74#issuecomment-448702821  

I don't have access to a machine right now to debug, but I think @kszucs is looking into it.   
  
I guess it is possible there is an unrelated memory corruption; if it's possible to get an ASAN/UBSAN build with this compiler configuration it may turn up something

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-01-22 18:07:20 UTC  
> Url: https://github.com/boostorg/regex/issues/74#issuecomment-577313018  

Closing because:  
  
* no further information has materialized.  
* XCode 6.4 is really rather outdated.
