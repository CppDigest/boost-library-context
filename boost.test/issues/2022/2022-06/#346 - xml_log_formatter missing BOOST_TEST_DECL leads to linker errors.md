# #346 - xml_log_formatter missing BOOST_TEST_DECL leads to linker errors [Open]

> Username: richleigh  
> Created at: 2022-06-29 14:48:44 UTC  
> Updated at: 2022-06-29 14:55:52 UTC  
> Url: https://github.com/boostorg/test/issues/346  

Boost 1.79.0  
GCC 11.2.0, -std=gnu++20  
Linux 3.10 x86_64  
  
Using a compiled shared object libboost_unit_test_framework.so and sources compiled with -DBOOST_TEST_DYN_LINK leads to undefined reference linker errors when creating/using boost::test::output::xml_log_formatter instances, that are not found when using compiler_log_formatter instances.  
  
The culprit seems to be that the definition of class xml_log_formatter is missing the BOOST_TEST_DECL  visibility macro that compiler_log_formatter has.

---

## Comment 1

> Username: richleigh  
> Created at: 2022-06-29 14:55:52 UTC  
> Url: https://github.com/boostorg/test/issues/346#issuecomment-1170086984  

Probably impacts junit_log_formatter, plain_report_formatter, and xml_report_formatter too, but haven't attempted to reproduce with those.
