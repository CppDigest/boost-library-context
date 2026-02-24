# #81 - "sign=space & pad_0s_until_width & alignment=right/internal/center" do not work together ("sign=space" is ignored) [Open]

> Username: jhcarl0814  
> Created at: 2021-02-16 12:48:22 UTC  
> Updated at: 2021-02-16 12:48:22 UTC  
> Url: https://github.com/boostorg/format/issues/81  

```c++  
#include<string>  
#include<iostream>  
#include<iomanip>  
#include<boost/format.hpp>  
  
class Rational {  
public:  
    Rational(int n, unsigned int d) : n_(n), d_(d) {}  
    Rational(int n, int d);    // convert denominator to unsigned  
    friend std::ostream& operator<<(std::ostream&, const Rational&);  
private:  
    int n_;               // numerator  
    unsigned int d_;      // denominator  
};  
  
Rational::Rational(int n, int d) : n_(n)  
{  
    if (d < 0) { n_ = -n_; d = -d; } // make the denominator always non-negative.  
    d_ = static_cast<unsigned int>(d);  
}  
  
std::ostream& operator<<(std::ostream& os, const Rational& r) {  
    using namespace std;  
    streamsize w = os.width(0); // width has to be zeroed before saving state.  
  
    boost::io::basic_oaltstringstream<char> oss;  
    oss.copyfmt(os);  
    oss << r.n_ << "/" << noshowpos << r.d_;  
  
    os.write(oss.begin(), oss.size());  
    return os;  
}  
  
int main(int argc, char* argv[])  
{  
    using namespace std;  
    using boost::format;  
    using boost::io::group;  
    using boost::io::str;  
  
    std::printf("[%8s][%8s]\n", "format", "printf");  
  
    std::printf("\nright:\n");//'+'/' ', right  
    std::cerr << boost::format("[%+8d]") % 12; std::printf("[%+8d]\n", 12);  
    std::cerr << boost::format("[% 8d]") % 12; std::printf("[% 8d]\n", 12);  
    std::cerr << boost::format("[%+08d]") % 12; std::printf("[%+08d]\n", 12);  
    std::cerr << boost::format("[% 08d]") % 12; std::printf("[% 08d] !!!\n", 12);  
  
    std::printf("\ninternal:\n");//'+'/' ', internal('_')  
    std::cerr << boost::format("[%_+8d]\n") % 12;  
    std::cerr << boost::format("[%_ 8d]\n") % 12;  
    std::cerr << boost::format("[%_+08d]\n") % 12;  
    std::cerr << boost::format("[%_ 08d] !!!\n") % 12;  
  
    std::printf("\ncenter:\n");//'+'/' ', center('=')  
    std::cerr << boost::format("[%=+8d]\n") % 12;  
    std::cerr << boost::format("[%= 8d]\n") % 12;  
    std::cerr << boost::format("[%=+08d]\n") % 12;  
    std::cerr << boost::format("[%= 08d] !!!\n") % 12;  
  
    std::printf("\nleft:\n");//'+'/' ', left('-')  
    std::cerr << boost::format("[%-+8d]") % 12; std::printf("[%-+8d]\n", 12);  
    std::cerr << boost::format("[%- 8d]") % 12; std::printf("[%- 8d]\n", 12);  
    std::cerr << boost::format("[%-+08d]") % 12; std::printf("[%-+08d]\n", 12);  
    std::cerr << boost::format("[%- 08d]") % 12; std::printf("[%- 08d]\n", 12);  
  
    Rational  r(16, 9);  
    std::printf("\nright:\n");  
    cerr << format("[%+8d]\n") % r;  
    cerr << format("[% 8d]\n") % r;  
    cerr << format("[%+08d]\n") % r;  
    cerr << format("[% 08d] !!!\n") % r;  
  
    std::printf("\ninternal:\n");  
    cerr << format("[%_+8d]\n") % r;  
    cerr << format("[%_ 8d]\n") % r;  
    cerr << format("[%_+08d]\n") % r;  
    cerr << format("[%_ 08d] !!!\n") % r;  
  
    std::printf("\ncenter:\n");  
    cerr << format("[%=+8d]\n") % r;  
    cerr << format("[%= 8d]\n") % r;  
    cerr << format("[%=+08d]\n") % r;  
    cerr << format("[%= 08d] !!!\n") % r;  
  
    std::printf("\nleft:\n");  
    cerr << format("[%-+8d]\n") % r;  
    cerr << format("[%- 8d]\n") % r;  
    cerr << format("[%-+08d]\n") % r;  
    cerr << format("[%- 08d]\n") % r;  
}  
```  
  
output (boost.format on the left, std::printf on the right, the differences is marked with `!!!`):    
```  
[  format][  printf]  
  
right:  
[     +12][     +12]  
[      12][      12]  
[+0000012][+0000012]  
[00000012][ 0000012] !!!  
  
internal:  
[+     12]  
[      12]  
[+0000012]  
[00000012] !!!  
  
center:  
[   +12  ]  
[    12  ]  
[+0000012]  
[00000012] !!!  
  
left:  
[+12     ][+12     ]  
[ 12     ][ 12     ]  
[+12     ][+12     ]  
[ 12     ][ 12     ]  
  
right:  
[   +16/9]  
[    16/9]  
[000+16/9]  
[000016/9] !!!  
  
internal:  
[   +16/9]  
[    16/9]  
[000+16/9]  
[000016/9] !!!  
  
center:  
[  +16/9 ]  
[   16/9 ]  
[000+16/9]  
[000016/9] !!!  
  
left:  
[+16/9   ]  
[ 16/9   ]  
[+16/9   ]  
[ 16/9   ]  
```
