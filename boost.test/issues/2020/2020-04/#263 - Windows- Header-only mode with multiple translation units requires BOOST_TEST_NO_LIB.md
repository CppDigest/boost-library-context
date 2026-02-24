# #263 - Windows: Header-only mode with multiple translation units requires BOOST_TEST_NO_LIB [Closed]

> Username: valiko-ua  
> Created at: 2020-04-03 22:37:15 UTC  
> Updated at: 2020-04-21 14:23:42 UTC  
> Closed at: 2020-04-06 17:32:58 UTC  
> Url: https://github.com/boostorg/test/issues/263  

According to [documentation](https://www.boost.org/doc/libs/1_72_0/libs/test/doc/html/boost_test/adv_scenarios/single_header_customizations/multiple_translation_units.html), `BOOST_TEST_NO_LIB` macro is not required when building multiple translation units in header-only mode, but I get the following error on Windows:  
  
```  
1>------ Rebuild All started: Project: MyTest, Configuration: Debug x64 ------  
1>test_main.cpp  
1>test2.cpp  
1>Generating Code...  
1>LINK : fatal error LNK1104: cannot open file 'libboost_unit_test_framework-vc142-mt-gd-x64-1_72.lib'  
1>Done building project "MyTest.vcxproj" -- FAILED.  
========== Rebuild All: 0 succeeded, 1 failed, 0 skipped ==========  
```  
  
Defining `BOOST_TEST_NO_LIB` macro in all additional translation units (the ones that include `boost/test/unit_test.hpp`) fixes the problem. If `boost/test/detail/config.hpp` cannot be enhanced somehow to fix this problem, then documentation should mention that `BOOST_TEST_NO_LIB` macro is required for the described case in all additional translation units (or just in all translation units).  
  
There is no problem when project contains only one translation unit (which includes `boost/test/included/unit_test.hpp`).  
  
### Minimum example  
Linker settings should not contain path to boost binary libraries to reproduce this problem.  
File `test_main.cpp`:  
```c++  
#define BOOST_TEST_MODULE My Test  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(test1)  
{  
    int i = 1;  
    BOOST_TEST(i == 1);  
    BOOST_TEST(i == 2);  
}  
```  
File `test2.cpp`:  
```c++  
//#define BOOST_TEST_NO_LIB // uncomment this line to fix link error  
#include <boost/test/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE(test2)  
{  
    int i = 2;  
    BOOST_TEST(i == 1);  
    BOOST_TEST(i == 2);  
}  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-04-03 23:16:44 UTC  
> Updated at: 2020-04-04 00:02:49 UTC  
> Url: https://github.com/boostorg/test/issues/263#issuecomment-608795127  

~~This is odd because there is a unit test covering this case on Windows: https://github.com/boostorg/test/blob/develop/test/Jamfile.v2#L137  
Do you know what is defining `BOOST_TEST_NO_LIB` ?~~  
  
Ok, now I think I know why the tests are not catching the issue. Thanks for the report

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2020-04-06 14:16:42 UTC  
> Url: https://github.com/boostorg/test/issues/263#issuecomment-609822537  

Fix available on branch `topic/GH-263-header-variant-test-no-lib`. If you have some time, it would be nice if you can give a try.

---

## Comment 3

> Username: valiko-ua  
> Created at: 2020-04-06 14:50:55 UTC  
> Url: https://github.com/boostorg/test/issues/263#issuecomment-609842358  

The fix works, thanks!

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2020-04-06 15:55:45 UTC  
> Url: https://github.com/boostorg/test/issues/263#issuecomment-609880674  

Thanks for the quick feedback!

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2020-04-06 17:32:47 UTC  
> Url: https://github.com/boostorg/test/issues/263#issuecomment-609933249  

Fix in master
