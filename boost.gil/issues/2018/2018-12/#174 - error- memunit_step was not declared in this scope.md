# #174 - error: memunit_step was not declared in this scope [Open]

> Username: mloskot  
> Created at: 2018-12-06 17:31:07 UTC  
> Updated at: 2019-11-07 10:00:58 UTC  
> Url: https://github.com/boostorg/gil/issues/174  

(follow-up to https://github.com/boostorg/gil/pull/173#discussion_r239542235)  
  
### Actual behavior  
  
Compile tests and examples with with `-DBOOST_GIL_USE_CONCEPT_CHECK=1`. For example, add the define to the GIL's top-level `Jamfile`:  
  
```  
project boost-gil  
    :  
    requirements  
        <define>BOOST_GIL_USE_CONCEPT_CHECK  
...  
```  
  
Run `b2 -d 2` to confirm the define is included in the compiler command line:  
  
Observer numerous compiler errors:  
  
```  
boost/gil/concepts/pixel_iterator.hpp:87:41: error: ‘memunit_step’ was not declared in this scope  
boost/gil/concepts/pixel_iterator.hpp:90:30: error: ‘memunit_advanced’ was not declared in this scope  
...  
```  
  
None of the `memunit_*` functions have ever been (forward) declared in any way, for example:  
  
https://github.com/boostorg/gil/blob/boost-1.35.0/include/boost/gil/gil_concept.hpp  
https://github.com/boostorg/gil/blob/boost-1.60.0/include/boost/gil/gil_concept.hpp  
https://github.com/boostorg/gil/blob/boost-1.65.0/include/boost/gil/gil_concept.hpp  
  
### Expected  behavior  
  
No compilation error.  
  
### Environment  
  
All relevant information like:  
  
- Boost version (see `<boost/version.hpp>`): from 1.35 until 1.69  
- Compiler version: GCC 5.5.0 but any C++11 compiler should fail

---

## Comment 1

> Username: mloskot  
> Created at: 2019-02-05 22:23:54 UTC  
> Url: https://github.com/boostorg/gil/issues/174#issuecomment-460828036  

This needs to be checked after #230 has been merged
