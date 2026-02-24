# #316 - Comparisons of basic_string_views with custom char_traits do not print out the string values [Open]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2021-09-02 17:30:08 UTC  
> Updated at: 2022-02-27 20:44:55 UTC  
> Url: https://github.com/boostorg/test/issues/316  

When I run the following tests  
```c++  
struct custom_char_traits : std::char_traits<char> {};  
  
BOOST_AUTO_TEST_CASE(custom_char_traits_compare)  
{  
    using sv = std::basic_string_view<char, custom_char_traits>;  
    sv sv1 = "sv1";  
    sv sv2 = "sv2";  
    BOOST_TEST(sv1 == sv2);  
}  
  
BOOST_AUTO_TEST_CASE(std_char_traits_compare)  
{  
    using sv = std::basic_string_view<char, std::char_traits<char>>;  
    sv sv1 = "sv1";  
    sv sv2 = "sv2";  
    BOOST_TEST(sv1 == sv2);  
}  
```  
I get the following output  
```  
*** 2 failures are detected in the test module "string view comparison"  
Running 2 test cases...  
example.cpp(14): error: in "custom_char_traits_compare": check sv1 == sv2 has failed  
example.cpp(22): error: in "std_char_traits_compare": check sv1 == sv2 has failed [sv1 != sv2]  
```  
  
The error message for `std_char_traits_compare` contains the string values, but the error for `custom_char_traits_compare` does not.   
 Boost Test should display the compared strings regardless of what traits they have.
