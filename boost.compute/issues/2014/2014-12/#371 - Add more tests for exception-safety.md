# #371 - Add more tests for exception-safety [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:50:08 UTC  
> Updated at: 2017-03-21 04:14:51 UTC  
> Url: https://github.com/boostorg/compute/issues/371  

Add more tests to ensure that Boost.Compute both correctly throws and recovers from exceptions.  
  
For example, add a test to `test_vector.cpp` which sets up a small vector with some values, tries to resize it larger than the device supports, ensures an exception is thrown, then checks that the original values are still correctly in place.  
##
