# #196 - boost::has_left_shift does not work with containers [Open]

> Username: davidgumberg  
> Created at: 2024-08-16 20:49:16 UTC  
> Updated at: 2024-08-16 20:49:16 UTC  
> Url: https://github.com/boostorg/type_traits/issues/196  

The following example is a minimal reproduction of this issue:  
  
```cpp  
#define BOOST_TEST_MODULE ExampleTestModule  
#include <boost/static_assert.hpp>  
#include <boost/test/included/unit_test.hpp>  
  
inline std::ostream& operator<<(std::ostream& os, const std::pair<int, int>& _) {  
    os << "Pairwise operator called!" << std::endl;  
    return os;  
}  
  
BOOST_STATIC_ASSERT(boost::has_left_shift<std::ostream&, const std::pair<int, int>&>::value);  
  
// If you comment out the above static assertion, the code compiles and this  
// test case prints, as expected "Pairwise operator called!"  
BOOST_AUTO_TEST_CASE(test_case1)  
{  
    std::pair<int, int> pair;  
    std::cout << pair;  
}  
```  
  
Compiling with gcc 14.2.1 results in:  
  
```  
ex.cpp:10:87: error: static assertion failed: boost::has_left_shift<std::ostream&, const std::pair<int, int>&>::value  
   10 | BOOST_STATIC_ASSERT(boost::has_left_shift<std::ostream&, const std::pair<int, int>&>::value);  
```  
  
I have similar results in clang 18.1.6:  
  
```  
ex.cpp:10:21: error: static assertion failed due to requirement 'boost::has_left_shift<std::basic_ostream<char, std::char_traits<char>> &, const std::pair<int, int> &, boost::binary_op_detail::dont_care>::value': boost::has_left_shift<std::ostream&, const std::pair<int, int>&>::value  
   10 | BOOST_STATIC_ASSERT(boost::has_left_shift<std::ostream&, const std::pair<int, int>&>::value);  
      |                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/boost/static_assert.hpp:71:55: note: expanded from macro 'BOOST_STATIC_ASSERT'  
   71 | #     define BOOST_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |     
```  
  
The same happens if you use a `std::vector<int>` instead.
