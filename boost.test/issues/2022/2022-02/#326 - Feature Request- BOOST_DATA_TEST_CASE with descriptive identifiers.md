# #326 - Feature Request: BOOST_DATA_TEST_CASE with descriptive identifiers [Open]

> Username: notdanhan  
> Created at: 2022-02-10 20:54:46 UTC  
> Updated at: 2022-02-27 19:47:02 UTC  
> Url: https://github.com/boostorg/test/issues/326  

would it be possible for there to exist a variant of BOOST_DATA_TEST_CASE where something like  
```c++  
  
BOOST_DATA_TEST_CASE(my_data_test_case,  
                       boost::unit_test::data::make({EMyEnum::val1, EMyEnum::val2, EMyEnum::val3}),  
                       values)  
{  
    //Do Something  
}  
```  
Could be identified using a syntax like  
```  
my_data_test_case/val1  
my_data_test_case/val2  
my_data_test_case/val3  
```  
or  
```  
my_data_test_case(val1)  
my_data_test_case(val2)  
my_data_test_case(val3)  
```  
instead of   
```  
my_data_test_case/_0  
my_data_test_case/_1  
my_data_test_case/_2  
```  
within the output from the test suite where val1, val2, val3, etc. represent the value produced by the given member of the dataset when using the insertion operator?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-02-27 19:37:12 UTC  
> Url: https://github.com/boostorg/test/issues/326#issuecomment-1053656617  

This would lead in test cases that are either not directly runable with the [`--run_test`](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/utf_reference/rt_param_reference/run_test.html) command line argument, or that would not lead to a valid test name. Consider for instance the cases where there is a combination of values for each test cases (using eg. [`grid`](https://www.boost.org/doc/libs/1_78_0/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation/operations.html#boost_test.tests_organization.test_cases.test_case_generation.operations.grid_cartesian_products)).  
  
With this in mind, Boost.Test considers the `my_data_test_case` as a unique test /suite/ (we can run all the tests from it) and of course we can run each test  individually. This would not be possible with the propose approach.  
  
Does this make sense?
