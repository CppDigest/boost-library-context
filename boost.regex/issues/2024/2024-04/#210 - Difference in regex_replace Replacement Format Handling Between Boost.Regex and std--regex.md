# #210 - Difference in regex_replace Replacement Format Handling Between Boost.Regex and std::regex [Open]

> Username: josuegomes  
> Created at: 2024-04-20 13:31:31 UTC  
> Updated at: 2024-04-20 13:31:31 UTC  
> Url: https://github.com/boostorg/regex/issues/210  

There is a difference in how the replacement format string is handled by regex_replace in Boost.Regex and std::regex.  
  
Consider the following example:  
  
```cpp  
#include <iostream>  
#include <regex>  
#include <boost/regex.hpp>  
  
int main() {  
    std::string s{"a:b:c"};  
    std::string rx{":"};  
    std::string r{"\\$&"};  
  
    // Using std::regex  
    std::string result_std = std::regex_replace(s, std::regex(rx), r);  
    std::cout << "std::regex result: " << result_std << std::endl;  // Outputs "a\:b\:c"  
  
    // Using Boost.Regex  
    std::string result_boost = boost::regex_replace(s, boost::regex(rx), r);  
    std::cout << "Boost.Regex result: " << result_boost << std::endl;  // Outputs "a$&b$&c"  
  
    return 0;  
}  
```  
  
Affects Boost.Regex 1.84. I didn't try other versions.  
  
Tested with GCC 13.2, clang 18.10 , cl 19.39.33523 and results are the same. Not tested with other versions.
