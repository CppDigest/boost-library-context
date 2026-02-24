# #260 - BOOST_TEST(a == b) failing [Open]

> Username: lfcarreon1  
> Created at: 2020-03-11 02:52:38 UTC  
> Updated at: 2025-01-16 17:18:22 UTC  
> Url: https://github.com/boostorg/test/issues/260  

When performing the following comparison with Boost.Test:  
std::u8string string_1{u8"abc"};  
std::u8string_view string_2{u8"abc"};  
BOOST_TEST(string_1 == string_2);  
  
It generates the following error message:  
usr/include/c++/9/bits/basic_string.h: In instantiation of ‘class std::__cxx11::basic_string<const char8_t, std::char_traits<const char8_t>, std::allocator<const char8_t> >’:  
/usr/local/include/boost/test/tools/cstring_comparison_op.hpp:75:1:   required from ‘static bool boost::test_tools::assertion::op::EQ<Lhs, Rhs, typename boost::enable_if_c<(boost::unit_test::is_cstring_comparable<Lhs>::value && boost::unit_test::is_cstring_comparable<Rhs>::value)>::type>::eval(const Lhs&, const Rhs&) [with Lhs = std::__cxx11::basic_string<char8_t>; Rhs = std::basic_string_view<char8_t>]’  
/usr/local/include/boost/test/tools/assertion.hpp:354:24:   required from ‘boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::value() const [with LExpr = boost::test_tools::assertion::value_expr<std::__cxx11::basic_string<char8_t>&>; Rhs = std::basic_string_view<char8_t>&; OP = boost::test_tools::assertion::op::EQ<std::__cxx11::basic_string<char8_t>, std::basic_string_view<char8_t>, void>; boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::result_type = boost::test_tools::assertion_result]’  
/usr/local/include/boost/test/tools/assertion.hpp:363:42:   required from ‘boost::test_tools::assertion_result boost::test_tools::assertion::binary_expr<Lhs, Rhs, OP>::evaluate(bool) const [with LExpr = boost::test_tools::assertion::value_expr<std::__cxx11::basic_string<char8_t>&>; Rhs = std::basic_string_view<char8_t>&; OP = boost::test_tools::assertion::op::EQ<std::__cxx11::basic_string<char8_t>, std::basic_string_view<char8_t>, void>]’  
  
Is there another way of fixing this issue other than changing the BOOST_TEST line to:  
BOOST_TEST((string_1 == string_2));

---

## Comment 1

> Username: ppmfloss  
> Created at: 2020-05-05 02:24:13 UTC  
> Url: https://github.com/boostorg/test/issues/260#issuecomment-623801769  

You can try one of these  
- ```BOOST_TEST(bool(string_1 == string_2));```  
- ```BOOST_TEST(static_cast<bool>(string_1 == string_2));```

---

## Comment 2

> Username: pborsutzki  
> Created at: 2025-01-16 17:18:21 UTC  
> Url: https://github.com/boostorg/test/issues/260#issuecomment-2596287999  

Looks like this could serve as a fix:  
  
``` cpp  
namespace boost::unit_test::ut_detail {  
  
template <>  
struct deduce_cstring_transform_impl< std::basic_string_view<char8_t, std::char_traits<char8_t> >, false > {  
private:  
    using sv_t = std::basic_string_view<char8_t, std::char_traits<char8_t> >;  
  
public:  
    using type = stringview_cstring_helper<char8_t, sv_t>;  
};  
  
class u8string_cstring_helper : public basic_cstring<char8_t> {  
public:  
    u8string_cstring_helper(std::basic_string<char8_t> const& sv)  
        : basic_cstring<char8_t>(const_cast<char8_t*>(sv.data()), sv.size())  
    {}  
};  
  
template <>  
struct deduce_cstring_transform_impl< std::basic_string<char8_t, std::char_traits<char8_t> >, false > {  
    using type = u8string_cstring_helper;  
};  
  
} // namespace boost::unit_test::ut_detail  
```  
  
Example: https://godbolt.org/z/ndha5W8j3
