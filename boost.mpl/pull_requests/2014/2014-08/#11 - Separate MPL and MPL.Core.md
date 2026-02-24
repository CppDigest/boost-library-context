# #11 Separate MPL and MPL.Core [Merged]

> Username: Lastique  
> Created at: 2014-08-31 11:04:05 UTC  
> Updated at: 2014-09-14 06:53:05 UTC  
> Merged at: 2014-09-10 20:36:48 UTC  
> Closed at: 2014-09-10 20:36:48 UTC  
> Url: https://github.com/boostorg/mpl/pull/11  

The pull request moves some core components to a separate sub-library Boost.MPL.Core. In particular, the following components are moved:  
- intergal constant wrappers and void_  
- arithmetic, logical, comparison and bitwise operations  
- diagnostic tools like print and BOOST_MPL_ASSERT  
- if_, eval_if, sizeof_, has_xxx, identity  
  
To remove the dependency of the core components on the TypeTraits library the workaround for Borland Compiler 5.9 has been removed from has_xxx.hpp. As a result MPL.Core headers only depend on Config and Preprocessor. MPL additionally depends on Core, TypeTraits, Predef, StaticAssert and Utility.  
  
The tests for the moved components were also moved to the sub-library. The testing headers moved from boost/mpl/aux_ to tests. Also created a readme file for GitHub project page.  
  
This pull request depends on fixing the Jamroot file (see https://github.com/boostorg/boost/pull/35) so that 'b2 headers' work correctly for the core headers (this is already done).

---
