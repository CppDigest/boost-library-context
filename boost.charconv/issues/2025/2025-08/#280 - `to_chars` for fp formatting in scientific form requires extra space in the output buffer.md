# #280 - `to_chars` for fp formatting in scientific form requires extra space in the output buffer [Closed]

> Username: dangelog  
> Created at: 2025-08-14 09:02:34 UTC  
> Updated at: 2025-08-14 11:57:07 UTC  
> Closed at: 2025-08-14 11:57:07 UTC  
> Url: https://github.com/boostorg/charconv/issues/280  

When formatting a positive `double` in scientific form with P digits of precision, P+7 characters should suffice in the output buffer: the number has the form `D.PPPPPPE±XXX` (digit, decimal point, P digits of precision, E, exponent sign, up to 3 digits for the exponent), which is P+7 characters long. Boost.Charconv however requires an extra character in the output buffer, and will error out if it's not provided.   
  
If I do provide room for the extra character, it then ends up being unused, so it shouldn't have been required to begin with.  
  
Testcase: https://gcc.godbolt.org/z/3ro8qjK7c  
  
```  
#include <iomanip>  
#include <iostream>  
#include <system_error>  
  
#include <numeric>  
#include <cmath>  
#include <cfloat>  
#include <climits>  
  
#ifdef USE_BOOST  
#  include <boost/charconv.hpp>  
#else  
#  include <charconv>  
#endif  
   
int main()  
{  
    constexpr double d = DBL_MIN;  
    constexpr int precision = 5;  
    // for scientific, precision + 7 should suffice:  
    // <digit> <.> <#precision digits> <e> <sign> <up to 3 digits>  
    //  1       2                       3   4      7  
    constexpr size_t buf_size = precision + 7;  
    char buf[buf_size];  
  
#ifdef USE_BOOST  
    auto result = boost::charconv::to_chars(buf, buf + buf_size, d, boost::charconv::chars_format::scientific, precision);  
    std::cout << "Using Boost: ";  
#else  
    auto result = std::            to_chars(buf, buf + buf_size, d, std::chars_format::scientific, precision);  
    std::cout << "Using std: ";  
#endif  
   
    if (result.ec != std::errc()) {  
        std::cout << "error: " << std::make_error_code(result.ec).message() << '\n';  
    } else {  
        std::string_view str(buf, result.ptr - buf);  
        std::cout << "result: " << std::quoted(str) << ", size: " << str.size() << '\n';  
    }  
}  
```
