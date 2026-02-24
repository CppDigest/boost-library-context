# #211 - Lack of support for C++17 [[fallthrough]] attribute with Intel 18 compiler [Closed]

> Username: awulkiew  
> Created at: 2018-01-29 23:32:30 UTC  
> Updated at: 2018-08-04 18:32:11 UTC  
> Closed at: 2018-08-04 18:32:11 UTC  
> Url: https://github.com/boostorg/config/issues/211  

[Intel compiler 18 supports `[[fallthrough]]` attribute](https://software.intel.com/en-us/articles/c17-features-supported-by-intel-c-compiler) but Config doesn't define `BOOST_FALTHROUGH` for this compiler. When compiled with [Linux Intel compiler 18.0 update 1 in gcc7 environment](http://www.boost.org/development/tests/develop/mibintc-develop.html) it seems that GCC `BOOST_FALLTHROUGH` definition is used which results in:  
  
    warning #1292: unknown attribute "fallthrough"  
  
This is only my guess based on the fact that with GCC the definition is:  
  
    #  define BOOST_FALLTHROUGH __attribute__((fallthrough))  
  
See e.g. [Boost.Geometry test results](http://www.boost.org/development/tests/develop/output/mibintc-develop-geometry-intel-linux-linux-warnings.html).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-23 17:28:12 UTC  
> Url: https://github.com/boostorg/config/issues/211#issuecomment-375741870  

Can confirm:  
http://www.boost.org/development/tests/develop/developer/output/mibintc-develop-boost-bin-v2-libs-beast-test-beast-websocket-accept-test-intel-linux-linux-debug-threadapi-pthread-threading-multi.html
