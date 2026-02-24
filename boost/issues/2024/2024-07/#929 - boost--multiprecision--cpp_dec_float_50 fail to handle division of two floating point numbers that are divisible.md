# #929 - boost::multiprecision::cpp_dec_float_50 fail to handle division of two floating point numbers that are divisible [Open]

> Username: Mike-debug  
> Created at: 2024-07-24 04:59:43 UTC  
> Updated at: 2024-07-24 05:02:27 UTC  
> Url: https://github.com/boostorg/boost/issues/929  

As it shows below, dividing two floating point numbers that are divisible by themselves results in a loss of precision.  
Is it a bug exists in the **boost of version 1.85.0.**  
``` cpp  
#include <iostream>  
#include <string>  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/algorithm/string.hpp>  
using namespace std;  
namespace bmp = boost::multiprecision;  
typedef bmp::cpp_dec_float_50 bfloat;  
static const bfloat BFLOAT0{"0.0"};  
  
void print_price(bfloat price);  
  
int main()  
{  
    bfloat amount{"158.94600000"};  
    bfloat quantity{"0.05900000"};  
    bfloat avg_price = (quantity == BFLOAT0) ? BFLOAT0 : amount / quantity;  
    print_price(avg_price);  
    bfloat correct_result{"2694"};  
    if (correct_result == avg_price) {  
        std::cout << "correct ans" << std::endl;  
    } else {  
        std::cout << "wrong ans" << std::endl;  
    }  
    /*  
    2694  
    2693.999999999999999999999999999999999999999999999999999999999999999999999978  
    wrong ans  
    */  
    return 0;  
}  
  
void print_price(bfloat price)  
{  
    std::cout << price << std::endl;  
    std::cout << price.str() << std::endl;  
}  
  
```
