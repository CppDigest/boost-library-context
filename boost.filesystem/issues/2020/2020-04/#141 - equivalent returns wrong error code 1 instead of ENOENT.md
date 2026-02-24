# #141 - equivalent returns wrong error code 1 instead of ENOENT [Closed]

> Username: Flamefire  
> Created at: 2020-04-24 16:33:03 UTC  
> Updated at: 2020-04-24 17:16:46 UTC  
> Closed at: 2020-04-24 17:16:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/141  

https://github.com/boostorg/filesystem/blob/8b138a9955956b2f18e34fc46c2587b8722459bc/src/operations.cpp#L1133 passes either true or false to the error function, so it can only be 1  
  
However tests check for ENOENT https://github.com/boostorg/filesystem/blob/8b138a9955956b2f18e34fc46c2587b8722459bc/test/operations_test.cpp#L1926  
  
This wasn't noticed because a wrong error code was not treated as a test failure, but I think it should. That would require handling getting the correct error code in the test.  
  
BTW: It is strange to use "not supported" as the error code on windows: https://github.com/boostorg/filesystem/blob/8b138a9955956b2f18e34fc46c2587b8722459bc/src/operations.cpp#L1179
