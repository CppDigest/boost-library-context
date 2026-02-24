# #388 - setting precision to 0 on ostream with fixed flag produces wrong result [Closed]

> Username: mgeck64  
> Created at: 2021-11-09 11:01:43 UTC  
> Updated at: 2022-02-13 09:06:55 UTC  
> Closed at: 2022-02-13 09:06:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/388  

The following program precisely describes the issue as well as reproducing it. I believe I have a fix for this and will submit a pull request for it.  
~~~  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
static const boost::multiprecision::cpp_bin_float_50 foo = 1.91111111111111;  
  
int main() {  
    // case 1 - prints foo as expected  
    std::cout << foo << std::endl;  
  
    // case 2 - erronously prints foo at max. precision (1.91111111111111...);  
    // what was expected was to print just the integer part of foo rounded up;  
    // i.e., 2  
    std::cout.setf(std::ios::fixed);  
    std::cout.precision(0);  
    std::cout << foo << std::endl;  
}  
~~~
