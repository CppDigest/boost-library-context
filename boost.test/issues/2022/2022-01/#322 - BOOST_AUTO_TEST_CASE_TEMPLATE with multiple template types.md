# #322 - BOOST_AUTO_TEST_CASE_TEMPLATE with multiple template types [Open]

> Username: jonesmz  
> Created at: 2022-01-11 18:46:19 UTC  
> Updated at: 2022-01-11 18:46:19 UTC  
> Url: https://github.com/boostorg/test/issues/322  

I find myself with a need to do something like:  
  
```  
using test_types_1 std::tuple<int,long,unsigned char> ;  
using test_types_2 = test_types_1;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE( my_test, T1, T2, test_types_1, test_types_2 )  
{  
  BOOST_TEST( sizeof(T1) == sizeof(T2) );  
}  
```  
Where each call to `my_test` is the cross-product of the two vectors of test types.  
  
As well as a need to do  
  
```  
using test_types_1 std::tuple<int,long,unsigned char> ;  
using test_types_2 = some_template_type_list<std::vector, std::array, std::set>;  
  
BOOST_AUTO_TEST_CASE_TEMPLATE( my_test, T1, template<typename ...> T2, test_types_1, test_types_2 )  
{  
  BOOST_TEST( T2<T1>.empty() );  
}  
```  
  
Where the template type provided to BOOST_AUTO_TEST_CASE_TEMPLATE is not fully specified, and is itself a template, so that one of the other template parameter types, or manually written code inside the test, can instantiate the template as part of the test.
