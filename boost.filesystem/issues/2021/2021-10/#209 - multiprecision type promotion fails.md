# #209 - multiprecision type promotion fails [Closed]

> Username: ianhbell  
> Created at: 2021-10-05 12:53:13 UTC  
> Updated at: 2021-10-05 12:54:56 UTC  
> Closed at: 2021-10-05 12:54:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/209  

I would like to use ``boost::multiprecision`` inside containers, which sort of works, but some of the type-promotion does not.  For instance this is valid code, but ``boost::multiprecision`` doesn't figure out the promotion needed to do the multiplication and I get pages of errors that originate with a message like:  
```  
<source>:8:11: error: invalid operands to binary expression ('std::complex<my_float>' (aka 'complex<number<backends::cpp_bin_float<50>>>') and 'double')  
    auto c = a*b;  
```  
This doesn't compile on any compiler I have tried (clang, G++, MSVC), with boost 1.64, 1.77.  Compiler explorer: https://godbolt.org/z/Wr1v3Wh5P  
  
```c++  
#include <boost/multiprecision/cpp_bin_float.hpp>  
#include <complex>  
  
int main()  
{  
    using my_float = boost::multiprecision::cpp_bin_float_50;  
    std::complex<my_float> a{1.2, 5.6};  
    double b = 3.4;  
    auto c = a*b;  
}  
```

---

## Comment 1

> Username: ianhbell  
> Created at: 2021-10-05 12:54:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/209#issuecomment-934385273  

Sorry wrong place to file issue...
