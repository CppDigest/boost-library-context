# #74 testing: run tests conditionally [Merged]

> Username: akrzemi1  
> Created at: 2015-06-02 11:55:28 UTC  
> Updated at: 2015-06-02 11:56:23 UTC  
> Merged at: 2015-06-02 11:56:21 UTC  
> Closed at: 2015-06-02 11:56:22 UTC  
> Url: https://github.com/boostorg/test/pull/74  

As an illustration, I configured three examples from documentation, so that they are only compiled when support for variadic macros is present on the compiler. I am using a feature from Boost.Config: http://www.boost.org/doc/libs/1_58_0/libs/config/doc/html/boost_config/build_config.html  
It uses predefined platform macros (rather than tester name) to detect the availability of the feature.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2015-06-02 11:56:23 UTC  
> Url: https://github.com/boostorg/test/pull/74#issuecomment-107927498  

Super, many thanks!

---
