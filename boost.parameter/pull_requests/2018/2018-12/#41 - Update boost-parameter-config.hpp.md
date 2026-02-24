# #41  Update boost/parameter/config.hpp [Closed]

> Username: CromwellEnage  
> Created at: 2018-12-22 09:51:57 UTC  
> Updated at: 2019-01-04 07:39:03 UTC  
> Closed at: 2019-01-04 07:35:53 UTC  
> Url: https://github.com/boostorg/parameter/pull/41  

* Raise BOOST_PARAMETER_MAX_ARITY to 20 for C++03 compilers.  
* Attempt to fix some regressions in the Boost develop test matrix by turning off perfect forwarding support for affected compilers.

---

## Comment 1

> Username: CromwellEnage  
> Created_at: 2018-12-24 07:08:40 UTC  
> Url: https://github.com/boostorg/parameter/pull/41#issuecomment-449695266  

I've backed out of the change in BOOST_PARAMETER_MAX_ARITY.  In my Boost.Log fork, attempting to modify <boost/log/utility/setup/file.hpp> by using BOOST_PARAMETER_BASIC_FUNCTION with BOOST_PARAMETER_MAX_ARITY set to 12 or higher causes several compilers in c++03 mode to run out of virtual memory.

---
