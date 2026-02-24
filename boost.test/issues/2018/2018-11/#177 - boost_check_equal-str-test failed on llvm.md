# #177 - boost_check_equal-str-test failed on llvm [Closed]

> Username: BinCaoWR  
> Created at: 2018-11-09 07:56:47 UTC  
> Updated at: 2019-02-08 17:25:42 UTC  
> Closed at: 2019-02-08 13:42:34 UTC  
> Url: https://github.com/boostorg/test/issues/177  

Test log:  
../libs/test/test/writing-test-ts/boost_check_equal-str-test.cpp(55): error: in "check_string_compare": check (void*)buf_ptr_cch != (void*)buf_array_cch has failed [0x800d3fd7 == 0x800d3fd7]  
  
Test code:  
char const* buf_ptr_cch     = "abc";  
char const  buf_array_cch[] = "abc";  
BOOST_TEST((void*)buf_ptr_cch != (void*)buf_array_cch);  
  
Potential solution:  
Add “<toolset>clang:<optimization>off” into boost.test-self-test() from libs/test/test/Jamfile.v2 to turn off the optimization for llvm. Verified the test can pass with this solution.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-04 07:03:27 UTC  
> Url: https://github.com/boostorg/test/issues/177#issuecomment-460148981  

Please check the branch `topic/GH-177-boost_check_equal-str-test-failure`, it should be fixed there.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-08 13:42:34 UTC  
> Url: https://github.com/boostorg/test/issues/177#issuecomment-461806527  

In master, closing. Thanks!
