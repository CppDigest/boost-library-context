# #5 Make Boost.Test work with RTTI off [Merged]

> Username: apolukhin  
> Created at: 2014-05-05 16:02:53 UTC  
> Updated at: 2014-06-17 17:48:05 UTC  
> Merged at: 2014-05-07 01:57:27 UTC  
> Closed at: 2014-05-07 01:57:27 UTC  
> Url: https://github.com/boostorg/test/pull/5  

This patch fixes GCC and CLANG compilation when RTTI is off. Tested on clang-3.0, clang-3.4. gcc-4.6  
  
This patch is essential for Boost.TypeIndex library which must be tested with RTTI off and RTTI on.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-05-05 19:49:47 UTC  
> Url: https://github.com/boostorg/test/pull/5#discussion_r12295958  

This line will create test cases with duplicate name, as BOOST_CURRENT_FUNCTION applies to generate_test_case_4_type::operator().   
  
I understand that without RTTI, we are unable to retrieve the name of the current type. Any chance to have at least the index of the current type?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-05-06 05:28:36 UTC  
> Url: https://github.com/boostorg/test/pull/5#issuecomment-42269048  

Actually it will output the name of the TestType:  
  
```  
#include <iostream>  
#include <boost/current_function.hpp>  
  
template <class TestType>  
struct generate_test_case_4_type  
{  
    void operator()() const {  
        std::cout << BOOST_CURRENT_FUNCTION;  
    }  
};  
  
struct print_me{};  
  
int main()  
{  
    generate_test_case_4_type<print_me> p;  
    p();  
}  
```  
  
That piece of code will output: `void generate_test_case_4_type<`**print_me**`>::operator()() const`  
**print_me** structure is in there, so there'll be no duplicate names.

---
