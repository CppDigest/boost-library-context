# #404 - Unexpected behavior with `--run_test` [Closed]

> Username: lballabio  
> Created at: 2023-12-05 17:12:48 UTC  
> Updated at: 2023-12-06 07:47:07 UTC  
> Closed at: 2023-12-06 07:47:07 UTC  
> Url: https://github.com/boostorg/test/issues/404  

With the following file:  
  
```  
#define BOOST_TEST_MODULE My Test  
#include <boost/test/included/unit_test.hpp>  
  
namespace utf = boost::unit_test;  
  
BOOST_AUTO_TEST_SUITE(first_suite)  
  
BOOST_AUTO_TEST_CASE(first_test)  
{  
  BOOST_TEST(1 == 1);  
}  
  
BOOST_AUTO_TEST_CASE(second_test, *utf::disabled())  
{  
  BOOST_TEST(2 == 0);  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
  
  
BOOST_AUTO_TEST_SUITE(second_suite)  
  
BOOST_AUTO_TEST_CASE(first_test)  
{  
  BOOST_TEST(4 == 4);  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
  
Running the test program without arguments doesn't run the disabled test, but running it with `--run_test=first_suite` does.  Is this the wanted behavior?  I can see how this behavior could be defended if one were to use `--run_test=first_suite/*`, but when passing just the name of the suite it looks counterintuitive.  
  
And if this is indeed intended, is there a way to tell the program "run this suite only and keep the disabled tests disabled"?  Thanks!

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2023-12-05 23:55:28 UTC  
> Url: https://github.com/boostorg/test/issues/404#issuecomment-1841825988  

Hi Luigi,  
  
That behaviour is intentional (more on this in this [section](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/runtime_config/test_unit_filtering.html)):  
  
* enabling/disabling propagates following the tree and overrides the default (the default here is `utf::disabled()` for `first_suite/second_test`)  
* you can selectively exclude tests with the `!`, which is ok if the amount of tests to exclude is not that big  
* you can use a common pattern (prefix, suffix, etc) for the tests to run within the suite and use a filtering with `*` based on the name  
* you can use labels or more [complicated preconditions](https://www.boost.org/doc/libs/1_83_0/libs/test/doc/html/boost_test/tests_organization/enabling.html#boost_test.tests_organization.enabling.example_descr1), which in turn can use command line arguments  
  
This does not directly answer your question, but the test filtering is actually quite flexible.

---

## Comment 2

> Username: lballabio  
> Created at: 2023-12-06 07:47:07 UTC  
> Url: https://github.com/boostorg/test/issues/404#issuecomment-1842270831  

Thanks!
