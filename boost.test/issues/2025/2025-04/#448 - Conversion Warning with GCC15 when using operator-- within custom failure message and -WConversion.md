# #448 - Conversion Warning with GCC15 when using operator<< within custom failure message and -WConversion [Open]

> Username: induras  
> Created at: 2025-04-28 07:40:19 UTC  
> Updated at: 2025-04-28 07:59:14 UTC  
> Url: https://github.com/boostorg/test/issues/448  

Starting with GCC15 ( when using -WConversion ) I get conversion warnings when using stream operator in custom failure messages.  
  
Example code from the docs also produces those warnings:  
  
[Boost Test](https://www.boost.org/doc/libs/1_88_0/libs/test/doc/html/boost_test/testing_tools/reports.html#boost_test.testing_tools.reports.custom_messages)  
  
```  
#define BOOST_TEST_MODULE boost_test_message  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE( test_message )  
{  
  const int a(1), b(2);  
  BOOST_TEST(a == b, "a should be equal to b: " << a << "!=" << b);  
  BOOST_TEST(a != 10, "value of a=" << a);  
}  
```  
  
[Compiler Explorer Example](https://compiler-explorer.com/z/3zMWcsPEh)  
  
Warning:  
  
```  
<source>: In member function 'void test_message::test_method()':  
<source>:8:62: warning: choosing 'boost::test_tools::tt_detail::assertion_evaluate_t<E>::operator boost::test_tools::assertion_result() [with E = boost::test_tools::assertion::binary_expr<boost::test_tools::assertion::value_expr<const int&>, const int&, boost::test_tools::assertion::op::EQ<int, int, void> >]' over 'boost::test_tools::assertion_result::assertion_result(const BoolConvertable&) [with BoolConvertable = boost::test_tools::tt_detail::assertion_evaluate_t<boost::test_tools::assertion::binary_expr<boost::test_tools::assertion::value_expr<const int&>, const int&, boost::test_tools::assertion::op::EQ<int, int, void> > >]' [-Wconversion]  
    8 |   BOOST_TEST(a == b, "a should be equal to b: " << a << "!=" << b);  
      |                                                              ^  
<source>:8:62: warning:   for conversion from 'boost::test_tools::tt_detail::assertion_evaluate_t<boost::test_tools::assertion::binary_expr<boost::test_tools::assertion::value_expr<const int&>, const int&, boost::test_tools::assertion::op::EQ<int, int, void> > >' to 'boost::test_tools::assertion_result' [-Wconversion]  
<source>:8:62: note:   because conversion sequence for the argument is better  
```
