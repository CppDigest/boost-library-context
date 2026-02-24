# #235 - std::uint64_t numbers above a certain value are not formatted correctly [Closed]

> Username: fedmos  
> Created at: 2024-08-07 13:11:32 UTC  
> Updated at: 2025-01-16 18:10:37 UTC  
> Closed at: 2025-01-16 18:10:37 UTC  
> Url: https://github.com/boostorg/locale/issues/235  

Boost version: 1.83.000  
Compiler : GCC 13.2.0  
   
std::uint64_t numbers above a certain value are not formatted correctly:   
in the following minimal example `first` is formatted correctly (commas are used as separators), while `second` is not correctly formatted (no commas are used)   
  
Example:  
  
```  
#include <iostream>  
#include <iomanip>  
#include <boost/format.hpp>  
#include <boost/locale.hpp>  
  
int main()  
{  
    boost::locale::generator gen;  
    gen.categories(boost::locale::category_t::convert | boost::locale::category_t::formatting);  
    auto locale = gen.generate("en_US.utf-8");  
      
    std::stringstream output;  
    output << boost::locale::as::number;  
    output << std::setprecision(6);  
    output.imbue(locale);  
      
    std::uint64_t first = 9223372036854U;  
    std::uint64_t second = 9223372036854775808U;  
      
    output << first << " " << second;  
    std::clog << output.str() << std::endl;  
    return 0;  
}  
```  
Console output:  
  
`9,223,372,036,854 9223372036854775808`

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-12-05 19:22:49 UTC  
> Url: https://github.com/boostorg/locale/issues/235#issuecomment-2521219041  

The problem is that ICU only supports formatting `int64_t` numbers  
  
- We use [`NumberFormat`](https://unicode-org.github.io/icu-docs/apidoc/dev/icu4c/classicu_1_1NumberFormat.html#aeb8a04a7121bcec7daaa02d2b0ac94be)  
- That accepts a [`Formattable`](https://unicode-org.github.io/icu-docs/apidoc/dev/icu4c/classicu_1_1Formattable.html)  
- And that is (among others) a `double` or `int64_t` but no `uint64_t`  
  
So any integer value above `INT32_MAX` will be formatted using the classic locale. This is in line with how Boost.Locale handles things in general: Do the best it can and gracefully fall back to less functionality. In this case it is using the classic format.  
  
I'm working on a solution
