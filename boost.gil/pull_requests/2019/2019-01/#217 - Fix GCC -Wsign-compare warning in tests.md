# #217 Fix GCC -Wsign-compare warning in tests [Merged]

> Username: mloskot  
> Created at: 2019-01-19 13:17:28 UTC  
> Updated at: 2019-03-29 20:29:14 UTC  
> Merged at: 2019-01-19 14:05:36 UTC  
> Closed at: 2019-01-19 14:05:36 UTC  
> Url: https://github.com/boostorg/gil/pull/217  

Fixes at least two warnings issued by channel tests due to  
comparison of unsigned channel value with signed integer 1:  
  
```  
../../boost/test/tools/assertion.hpp:72:13:  
  warning: comparison between signed and unsigned  
    integer expressions [-Wsign-compare]  
```  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: GCC 5.5  
- Build settings: `<toolset>gcc:<cxxflags>"-std=c++11"`  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
------  
  
GCC warnings this PR fixes are:  
  
```  
In file included from ../../boost/test/test_tools.hpp:53:0,  
                 from ../../boost/test/unit_test.hpp:18,  
                 from test/gil_test_common.hpp:24,  
                 from test/channel/algorithm_channel_arithmetic.cpp:14:  
../../boost/test/tools/assertion.hpp: In instantiation of ‘static boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::result_type boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::eval(const Lhs&, const Rhs&) [with Lhs = unsigned int; Rhs = int; Enabler = void; boost::test_tools::assertion::op::EQ<Lhs, Rhs, Enabler>::result_type = bool]’:  
../../boost/test/tools/assertion.hpp:354:24:   required from ‘boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::value() const [with LExpr = boost::test_tools::assertion::value_expr<unsigned int&>; Rhs = int; OP = boost::test_tools::assertion::op::EQ<unsigned int, int, void>; boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type = bool]’  
../../boost/test/tools/assertion.hpp:363:47:   required from ‘boost::test_tools::assertion_result boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::evaluate(bool) const [with LExpr = boost::test_tools::assertion::value_expr<unsigned int&>; Rhs = int; OP = boost::test_tools::assertion::op::EQ<unsigned int, int, void>]’  
test/channel/algorithm_channel_arithmetic.cpp:46:5:   required from ‘void test_channel_arithmetic_mutable(mpl_::true_) [with ChannelFixtureBase = boost::gil::test::fixture::channel_value<unsigned int>; mpl_::true_ = mpl_::bool_<true>]’  
test/channel/algorithm_channel_arithmetic.cpp:75:56:   required from ‘void test_channel_arithmetic() [with ChannelFixtureBase = boost::gil::test::fixture::channel_value<unsigned int>]’  
test/channel/algorithm_channel_arithmetic.cpp:81:39:   required from ‘void channel_value<Channel>::test_method() [with Channel = unsigned int]’  
test/channel/algorithm_channel_arithmetic.cpp:78:1:   [ skipping 4 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../../boost/function/function_template.hpp:720:7:   required from ‘boost::function0<R>::function0(Functor, typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type) [with Functor = boost::unit_test::ut_detail::test_case_template_invoker<channel_value_invoker, unsigned int>; R = void; typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type = int]’  
../../boost/function/function_template.hpp:1068:16:   required from ‘boost::function<R()>::function(Functor, typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type) [with Functor = boost::unit_test::ut_detail::test_case_template_invoker<channel_value_invoker, unsigned int>; R = void; typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type = int]’  
../../boost/test/tree/test_case_template.hpp:98:9:   required from ‘void boost::unit_test::ut_detail::generate_test_case_4_type<Generator, TestCaseTemplate>::operator()(boost::mpl::identity<TestType>) [with TestType = unsigned int; Generator = boost::unit_test::ut_detail::template_test_case_gen<channel_value_invoker, std::tuple<unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> > > >; TestCaseTemplate = channel_value_invoker]’  
../../boost/test/tree/test_case_template.hpp:166:22:   required from ‘void boost::unit_test::ut_detail::template_test_case_gen<TestCaseTemplate, std::tuple<_Elements ...> >::for_each(F&, boost::unit_test::ut_detail::template_test_case_gen<TestCaseTemplate, std::tuple<_Elements ...> >::seq<Is ...>) [with tuple_t = std::tuple<unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> > >; F = boost::unit_test::ut_detail::generate_test_case_4_type<boost::unit_test::ut_detail::template_test_case_gen<channel_value_invoker, std::tuple<unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> > > >, channel_value_invoker>; int ...Is = {0, 1, 2, 3, 4, 5, 6, 7}; TestCaseTemplate = channel_value_invoker; tuple_parameter_pack = {unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> >}]’  
../../boost/test/tree/test_case_template.hpp:180:9:   required from ‘boost::unit_test::ut_detail::template_test_case_gen<TestCaseTemplate, std::tuple<_Elements ...> >::template_test_case_gen(boost::unit_test::const_string, boost::unit_test::const_string, std::size_t) [with TestCaseTemplate = channel_value_invoker; tuple_parameter_pack = {unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> >}; boost::unit_test::const_string = boost::unit_test::basic_cstring<const char>; std::size_t = long unsigned int]’  
test/channel/algorithm_channel_arithmetic.cpp:78:1:   required from here  
../../boost/test/tools/assertion.hpp:72:13: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]git  
     action( ==, EQ, != )                    \  
             ^  
../../boost/test/tools/assertion.hpp:144:20: note: in definition of macro ‘DEFINE_CONST_OPER’  
         return lhs oper rhs;                        \  
                    ^  
../../boost/test/tools/assertion.hpp:93:5: note: in expansion of macro ‘BOOST_TEST_FOR_EACH_COMP_OP’  
     BOOST_TEST_FOR_EACH_COMP_OP(action)     \  
     ^  
../../boost/test/tools/assertion.hpp:163:1: note: in expansion of macro ‘BOOST_TEST_FOR_EACH_CONST_OP’  
 BOOST_TEST_FOR_EACH_CONST_OP( DEFINE_CONST_OPER )  
 ^  
```  
  
```  
In file included from ../../boost/test/test_tools.hpp:53:0,  
                 from ../../boost/test/unit_test.hpp:18,  
                 from test/gil_test_common.hpp:24,  
                 from test/channel/algorithm_channel_relation.cpp:12:  
../../boost/test/tools/assertion.hpp: In instantiation of ‘static boost::test_tools::assertion::op::NE<Lhs, Rhs, Enabler>::result_type boost::test_tools::assertion::op::NE<Lhs, Rhs, Enabler>::eval(const Lhs&, const Rhs&) [with Lhs = unsigned int; Rhs = int; Enabler = void; boost::test_tools::assertion::op::NE<Lhs, Rhs, Enabler>::result_type = bool]’:  
../../boost/test/tools/assertion.hpp:354:24:   required from ‘boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::value() const [with LExpr = boost::test_tools::assertion::value_expr<unsigned int&>; Rhs = int; OP = boost::test_tools::assertion::op::NE<unsigned int, int, void>; boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type = bool]’  
../../boost/test/tools/assertion.hpp:363:47:   required from ‘boost::test_tools::assertion_result boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::evaluate(bool) const [with LExpr = boost::test_tools::assertion::value_expr<unsigned int&>; Rhs = int; OP = boost::test_tools::assertion::op::NE<unsigned int, int, void>]’  
test/channel/algorithm_channel_relation.cpp:28:5:   required from ‘void test_channel_relation() [with ChannelFixtureBase = boost::gil::test::fixture::channel_value<unsigned int>]’  
test/channel/algorithm_channel_relation.cpp:34:37:   required from ‘void channel_value<Channel>::test_method() [with Channel = unsigned int]’  
test/channel/algorithm_channel_relation.cpp:31:1:   required from ‘static void channel_value_invoker::run(boost::type<TestType>*) [with TestType = unsigned int]’  
../../boost/test/tree/test_case_template.hpp:67:52:   [ skipping 3 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
../../boost/function/function_template.hpp:720:7:   required from ‘boost::function0<R>::function0(Functor, typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type) [with Functor = boost::unit_test::ut_detail::test_case_template_invoker<channel_value_invoker, unsigned int>; R = void; typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type = int]’  
../../boost/function/function_template.hpp:1068:16:   required from ‘boost::function<R()>::function(Functor, typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type) [with Functor = boost::unit_test::ut_detail::test_case_template_invoker<channel_value_invoker, unsigned int>; R = void; typename boost::enable_if_<(! boost::is_integral<Functor>::value), int>::type = int]’  
../../boost/test/tree/test_case_template.hpp:98:9:   required from ‘void boost::unit_test::ut_detail::generate_test_case_4_type<Generator, TestCaseTemplate>::operator()(boost::mpl::identity<TestType>) [with TestType = unsigned int; Generator = boost::unit_test::ut_detail::template_test_case_gen<channel_value_invoker, std::tuple<unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> > > >; TestCaseTemplate = channel_value_invoker]’  
../../boost/test/tree/test_case_template.hpp:166:22:   required from ‘void boost::unit_test::ut_detail::template_test_case_gen<TestCaseTemplate, std::tuple<_Elements ...> >::for_each(F&, boost::unit_test::ut_detail::template_test_case_gen<TestCaseTemplate, std::tuple<_Elements ...> >::seq<Is ...>) [with tuple_t = std::tuple<unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> > >; F = boost::unit_test::ut_detail::generate_test_case_4_type<boost::unit_test::ut_detail::template_test_case_gen<channel_value_invoker, std::tuple<unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> > > >, channel_value_invoker>; int ...Is = {0, 1, 2, 3, 4, 5, 6, 7}; TestCaseTemplate = channel_value_invoker; tuple_parameter_pack = {unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> >}]’  
../../boost/test/tree/test_case_template.hpp:180:9:   required from ‘boost::unit_test::ut_detail::template_test_case_gen<TestCaseTemplate, std::tuple<_Elements ...> >::template_test_case_gen(boost::unit_test::const_string, boost::unit_test::const_string, std::size_t) [with TestCaseTemplate = channel_value_invoker; tuple_parameter_pack = {unsigned char, signed char, short unsigned int, short int, unsigned int, int, boost::gil::scoped_channel_value<float, boost::gil::float_point_zero<float>, boost::gil::float_point_one<float> >, boost::gil::scoped_channel_value<double, boost::gil::float_point_zero<double>, boost::gil::float_point_one<double> >}; boost::unit_test::const_string = boost::unit_test::basic_cstring<const char>; std::size_t = long unsigned int]’  
test/channel/algorithm_channel_relation.cpp:31:1:   required from here  
../../boost/test/tools/assertion.hpp:73:13: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]  
     action( !=, NE, == )                    \  
             ^  
../../boost/test/tools/assertion.hpp:144:20: note: in definition of macro ‘DEFINE_CONST_OPER’  
         return lhs oper rhs;                        \  
                    ^  
../../boost/test/tools/assertion.hpp:93:5: note: in expansion of macro ‘BOOST_TEST_FOR_EACH_COMP_OP’  
     BOOST_TEST_FOR_EACH_COMP_OP(action)     \  
     ^  
../../boost/test/tools/assertion.hpp:163:1: note: in expansion of macro ‘BOOST_TEST_FOR_EACH_CONST_OP’  
 BOOST_TEST_FOR_EACH_CONST_OP( DEFINE_CONST_OPER )  
 ^  
```

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-01-19 14:05:30 UTC  
> Url: https://github.com/boostorg/gil/pull/217#issuecomment-455782929  

Looks like the warning is gone from the CI builds as well.

---
