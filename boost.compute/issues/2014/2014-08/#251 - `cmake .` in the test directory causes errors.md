# #251 - `cmake .` in the test directory causes errors [Closed]

> Username: edbaunton  
> Created at: 2014-08-26 10:54:03 UTC  
> Updated at: 2014-08-28 18:05:08 UTC  
> Closed at: 2014-08-28 18:05:08 UTC  
> Url: https://github.com/boostorg/compute/issues/251  

```  
cd test && cmake  
```  
  
yields:  
  
```  
-- Boost version: 1.55.0  
-- Found the following Boost libraries:  
--   unit_test_framework  
CMake Error at CMakeLists.txt:16 (if):  
  if given arguments:  
  
"AND" "Clang" "STREQUAL" "GNU"  
  
Unknown arguments specified  
```  
  
Is this my misusage? Otherwise, I believe it needs to check whether `BOOST_COMPUTE_ENABLE_COVERAGE` is defined before testing its actual value?  
  
Something like  
  
`if ( DEFINED BOOST_COMPUTE_ENABLE_COVERAGE AND ...`

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-08-26 14:02:51 UTC  
> Updated at: 2014-08-26 14:08:31 UTC  
> Url: https://github.com/boostorg/compute/issues/251#issuecomment-53424507  

Yeah, `cmake` is usually run in a separate build directory instead of one of the source directories. See the developers guide in the wiki here: https://github.com/kylelutz/compute/wiki/Developer's-Guide. It contains instructions for running cmake and building the tests.  
  
Hope this helps, let me know if you run into any issues.
