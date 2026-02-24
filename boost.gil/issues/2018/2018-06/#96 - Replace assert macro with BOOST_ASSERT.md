# #96 - Replace assert macro with BOOST_ASSERT [Closed]

> Username: mloskot  
> Created at: 2018-06-19 20:46:18 UTC  
> Updated at: 2019-01-12 09:21:02 UTC  
> Closed at: 2019-01-12 09:21:02 UTC  
> Url: https://github.com/boostorg/gil/issues/96  

This is a C++ common sense and to respect GIL user requirements and policies as well as Boost own guidelines, https://www.boost.org/development/requirements.html  
  
> Avoid C's assert macro and use Boost's BOOST_ASSERT macro (in boost/assert.hpp ) instead.  
> It is more configurable. Use BOOST_ASSERT in public headers and in library source code  
> (for separately compiled libraries). Use of C's assert macro is ok in examples and in documentation.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-06-25 11:39:24 UTC  
> Url: https://github.com/boostorg/gil/issues/96#issuecomment-399922208  

Postponing after The Grand Merge as not get it done on time for Boost 1.68 (/cc @stefanseefeld)
