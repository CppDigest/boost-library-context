# #313 - unit_test.hpp successfully compiles invalid code [Open]

> Username: zethon  
> Created at: 2021-07-09 00:47:29 UTC  
> Updated at: 2021-07-09 00:47:48 UTC  
> Url: https://github.com/boostorg/test/issues/313  

I'm resubmitting this bug because the [last one](https://github.com/boostorg/test/issues/312) was closed and I'm afraid the nature of the issue was not clear.  
  
Consider this invalid code:   
```  
BOOST_TEST(1 = somestring.size());  
```  
  
Assignment to a literal should be a compilation error, however the code above compiles successfully when using unit_test.hpp.  
  
See: https://gcc.godbolt.org/z/MT8xrdesh
