# #293 - [patch] Clang 10 gives "marked as unused but used" warning on BOOST_AUTO_TEST_CASE [Open]

> Username: jm-mikkelsen  
> Created at: 2020-11-19 11:12:22 UTC  
> Updated at: 2020-12-09 13:49:37 UTC  
> Url: https://github.com/boostorg/test/issues/293  

Compiling on FreeBSD 12.2 using Clang 10 and `-Wall -Wextra -Werror` gives this error when expanding `BOOST_AUTO_TEST_CASE` and other macros:  
  
    'unit_test_log' was marked unused but was used [-Werror,-Wused-but-marked-unused]  
  
Attached is a patch which resolves the issue for me by removing the unused attribute from singleton references created by `BOOST_TEST_SINGLETON_INST` and instead having an inline function with an unused attribute that references the singleton.  
  
[patch-boost_test_detail_global_typedef_hpp.txt](https://github.com/boostorg/test/files/5566345/patch-boost_test_detail_global_typedef_hpp.txt)
