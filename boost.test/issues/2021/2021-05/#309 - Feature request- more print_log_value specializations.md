# #309 - Feature request: more print_log_value specializations [Closed]

> Username: mikeiovine  
> Created at: 2021-05-08 18:52:57 UTC  
> Updated at: 2022-12-08 20:06:08 UTC  
> Closed at: 2022-12-08 20:06:08 UTC  
> Url: https://github.com/boostorg/test/issues/309  

In my project, I have to test for equality of `std::pair`s. In other testing frameworks, this is very easy. For instance, in GoogleTest, this works: `ASSERT_EQ(std::make_pair(...), std::make_pair(...))`.  
  
This task turns out to be very awkward in Boost. The [docs](https://www.boost.org/doc/libs/1_76_0/libs/test/doc/html/boost_test/test_output/test_tools_support_for_logging/testing_tool_output_disable.html) recommend defining `operator<<` or `boost_test_print_type`. Unfortunately, these need to be defined in the `std` namespace, or else the function lookup will fail. Extending the namespace `std` with a new function definition is technically undefined behavior.   
  
For testing two `std::pair`s, it's easy (albeit slightly annoying) to just test them element-wise, e.g. with `BOOST_CHECK_EQUAL(pair1.first, pair2.first)`. However, this option is not available for comparing collections of pairs with `BOOST_TEST(collection1 == collection2, boost::test_tools::per_element())`  
  
The following alternative solution works:  
```cpp  
namespace boost {  
namespace test_tools {  
namespace tt_detail {  
  
template <class T, class U>  
struct print_log_value<std::pair<T, U>> {  
  void operator()(std::ostream &os, std::pair<T, U> p) {  
    os << "{" << p.first << ", " << p.second << "}";  
  }  
};  
  
}  // namespace tt_detail  
}  // namespace test_tools  
}  // namespace boost  
  
```  
Injecting a specialization into a `detail` namespace feels equally hacky.   
  
I think adding this specialization for `std::pair` would be very convenient.   
  
It would also be helpful to add a specialization for `std::optional` and `std::tuple`, though I'm not sure how these would work with respect to backwards compatibility since these are not available in C++03.
