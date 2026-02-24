# #306 - Switching to header-only variant [Closed]

> Username: eldiener  
> Created at: 2021-03-23 02:01:05 UTC  
> Updated at: 2022-03-08 17:54:48 UTC  
> Closed at: 2022-03-08 17:54:48 UTC  
> Url: https://github.com/boostorg/test/issues/306  

I would like to switch from the static library variant to the header-only variant, but I am unable to figure out how to do this from my current Boost.Test setup. In my current Boost.Test setup I have:  
  
```  
#include <boost/test/unit_test.hpp>  
void test_data_predicate_function() { lots of code with various Boost.Test macros... }  
boost::unit_test::test_suite* init_unit_test_suite( int, char* [] )  
{  
    boost::unit_test::test_suite* test = BOOST_TEST_SUITE( "Property Test Data Predicate Suite" );  
    test->add( BOOST_TEST_CASE( &test_data_predicate_function ) );  
    return test;  
}  
```  
  
I believe this is using the static library variant, but it is hard to tell because your document on the "Usage Variants" do not correspond to the code above in any way, but I know the code is working. So how to I switch from the above to the header-only variant with the minimum amount of changes ? I only need a single test case in a single source .cpp file, as shown above, and not anything fancier.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-02-27 01:21:23 UTC  
> Url: https://github.com/boostorg/test/issues/306#issuecomment-1052902822  

The [header only](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/usage_variants.html#boost_test.usage_variants.single_header) variant uses   
  
```  
#include <boost/test/included/unit_test.hpp>  
```  
  
while the other 2 variants use  
```  
#include <boost/test/unit_test.hpp>  
```  
  
If you want to keep the initialization function for the header variant, then the procedure is described [here](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/adv_scenarios/single_header_customizations/init_func.html).  
  
However I strongly suggest you get rid of the `init_unit_test_suite` in favor of the [automatic registration](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/tests_organization/test_cases/test_organization_nullary.html#boost_test.tests_organization.test_cases.test_organization_nullary.automated_registration): in most cases the custom init is not needed.   
  
Does it answer the question?

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2022-03-08 17:54:48 UTC  
> Url: https://github.com/boostorg/test/issues/306#issuecomment-1062048316  

I emphasized the differences for the header variant in the documentation through https://github.com/boostorg/test/pull/337 rev https://github.com/boostorg/test/commit/b52b0ea6429c393485f8ea27ab5edd678823e65a.
