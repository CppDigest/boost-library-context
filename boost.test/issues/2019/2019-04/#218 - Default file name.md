# #218 - Default file name [Closed]

> Username: diorcety  
> Created at: 2019-04-19 13:27:23 UTC  
> Updated at: 2019-08-05 19:03:43 UTC  
> Closed at: 2019-08-05 19:03:43 UTC  
> Url: https://github.com/boostorg/test/issues/218  

In the [documentation](https://www.boost.org/doc/libs/1_65_0/libs/test/doc/html/boost_test/test_output/log_formats/log_junit_format.html) it is written:  
  
> This format defaults its log level to General information and its default stream to a file named after master test suite  
  
With the following example  
  
```  
#define BOOST_TEST_MODULE my master test suite name  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE( free_test_function )  
{  
  BOOST_TEST( true /* test assertion */ );  
}  
  
```  
  
This is not true. The file is named Master_Test_Suite.xml. It seems that the streams are initialized before the call to init_unit_test (the file is already created when the debugger reachs the breakpoint in this function).  
  
The workaround is to use the following code  
```  
#define BOOST_TEST_MODULE my master test suite name  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_NO_MAIN  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE( free_test_function )  
{  
  BOOST_TEST( true /* test assertion */ );  
}  
  
int main(int argc, char *argv[]) {  
    init_unit_test();  
    return ::boost::unit_test::unit_test_main(init_unit_test, argc, argv);  
}  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-07-22 19:32:49 UTC  
> Url: https://github.com/boostorg/test/issues/218#issuecomment-513922546  

Fix in branch `topic/GH-218-outstream-default-filename`. It would be good if you can give a try.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-08-05 19:03:43 UTC  
> Url: https://github.com/boostorg/test/issues/218#issuecomment-518358674  

It is now on master and should be part of 1.71 release. Cheers!
