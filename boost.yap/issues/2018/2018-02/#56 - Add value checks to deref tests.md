# #56 - Add value checks to deref tests [Closed]

> Username: tzlaine  
> Created at: 2018-02-19 21:07:26 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Closed at: 2018-02-26 02:44:26 UTC  
> Url: https://github.com/boostorg/yap/issues/56  

deref.cpp:  
  
- the test cases in this file only show pass/fail, they  
  don't indicate the actual type returned by deref, which  
  will make debugging failures harder.  I typically use  
  BOOST_MPL_ASSERT((is_same<X, Y>));  specifically for  
  this reason (even when static_assert is available).  
- Actually, in general, these tests need to check both  
  the type and the value.  Checking just the type is  
  insufficient.
