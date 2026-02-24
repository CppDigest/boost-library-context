# #753 - boost::get overloads for boost::spirit::x3::variant should at least lack BOOST_NOEXCEPT [Open]

> Username: blashyrkh-ravendark  
> Created at: 2023-01-27 15:50:35 UTC  
> Updated at: 2025-05-09 23:04:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/753  

Since `boost::get` may throw `boost::bad_get` (in the event of a failed application on the given arg), all `boost::get` overloads for refs or ptrs to `boost::spirit::x3::variant` objects in `boost/spirit/home/x3/support/ast/variant.hpp` should at least lack `BOOST_NOEXCEPT` (which leads to `std::terminate` invocation when a function indeed throws).  
  
Example:  
  
```cpp  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
int main()  
{  
    x3::variant<bool, int> variant {5}; // Program crash  
    // boost::variant<bool, int> variant {5}; // No issue  
  
    try {  
        const auto v = boost::get<bool>(variant);  
    } catch(const boost::bad_get& e) {  
        std::cerr << "boost bad_get: " << e.what() << std::endl;  
        return 1;  
    } catch(const std::exception& e) {  
        std::cerr << "std exception: " << e.what() << std::endl;  
        return 1;  
    } catch(...) {  
        std::cerr << "Unknown error";  
        return 1;  
    }  
  
    return 0;  
}  
```
