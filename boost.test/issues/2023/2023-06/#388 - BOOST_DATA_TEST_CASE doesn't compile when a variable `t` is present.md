# #388 - BOOST_DATA_TEST_CASE doesn't compile when a variable `t` is present [Open]

> Username: circuitPolys  
> Created at: 2023-06-25 02:04:19 UTC  
> Updated at: 2023-06-26 15:55:29 UTC  
> Url: https://github.com/boostorg/test/issues/388  

For a minimal working example, consider the first BOOST_DATA_TEST_CASE example that appears in the ["Operations on datasets"](https://www.boost.org/doc/libs/1_82_0/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation/operations.html#boost_test.tests_organization.test_cases.test_case_generation.operations.joins.example_descr) page of the documentation. This example will not compile if the variable `var` is renamed to `t`. It seems that only `t` is affected, because the example compiles fine if `t` is replaced by any other letter.  
  
I have [asked about this on StackOverflow](https://stackoverflow.com/questions/76548435/a-simple-boost-data-test-doesnt-compile-if-an-argument-is-named-t/76548600#76548600) where the user "sehe" identified what is causing the errors: there is a line `test1case t;` in the expansion of the macro which is creating a conflict.
