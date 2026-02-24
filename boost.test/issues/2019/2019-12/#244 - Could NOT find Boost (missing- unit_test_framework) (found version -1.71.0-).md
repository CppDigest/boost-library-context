# #244 - Could NOT find Boost (missing: unit_test_framework) (found version "1.71.0") [Closed]

> Username: Amjad95  
> Created at: 2019-12-05 06:26:14 UTC  
> Updated at: 2019-12-17 19:28:51 UTC  
> Closed at: 2019-12-17 19:28:50 UTC  
> Url: https://github.com/boostorg/test/issues/244  

I need to use boost unit_test_framework, and what I am doing to find boost and the required libs is:  
 find_package(Boost REQUIRED COMPONENTS unit_test_framework)  
target_link_libraries (Boost_Tests_run Boost::unit_test_framework)  
 I get this error: Could NOT find Boost (missing: unit_test_framework) (found version "1.71.0")  
Note: I have built the boost libs!  
  
What can I do to fix it?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-12-17 19:28:50 UTC  
> Url: https://github.com/boostorg/test/issues/244#issuecomment-566712845  

@glenfe Thanks for transferring the issue here!  
  
@Amjad95 I believe you found a resolution of this in https://github.com/boostorg/test/issues/240, I am closing the issue. Feel free to reopen if needed.
