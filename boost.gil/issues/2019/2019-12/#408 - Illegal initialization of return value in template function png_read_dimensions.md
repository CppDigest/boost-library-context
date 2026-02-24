# #408 - Illegal initialization of return value in template function png_read_dimensions [Closed]

> Username: Dgroholsky  
> Created at: 2019-12-13 17:11:38 UTC  
> Updated at: 2019-12-14 18:39:59 UTC  
> Closed at: 2019-12-14 18:39:59 UTC  
> Url: https://github.com/boostorg/gil/issues/408  

### Actual behavior  
Compilation error  
  
```  
1>D:\Credo2000\GIT\Libs\boost_1_72_0\boost\gil\extension\io\png\old.hpp(23,27):   
error C2398: Element '1': conversion from 'unsigned int' to 'T' requires a narrowing conversion  
1>        with  
1>        [  
1>            T=ptrdiff_t  
1>        ]  
```  
  
**png_read_dimensions** return type is **point_t**, which is, actually, **point<std::ptrdiff_t>** with **ptrdiff_t = int**. But **backend._info._width** for png is **png_uint_32** (other graphic formats, have signed int type for dimensions).  
  
### Expected  behavior  
  
Compiling without errors. (Only win32)  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <boost/gil.hpp>  
namespace gil = boost::gil;  
 void Tile::load(char const * fileName)  
 {  
      auto sz = png_read_dimensions(fileName);  
 }  
int main  
{  
     Tile::load("D:/xxx")  
}  
```  
  
### Environment  
MSVS2019  
Toolset v142  
Windows SDK 10.0.17763.0  
Win32/debug  
/experimental:external /external:anglebrackets /external:W0 /permissive- /std:c++17 /Zc:twoPhase- /MP  
  
- Version (Git ref or `<boost/version.hpp>`): #define BOOST_VERSION 107200

---

## Comment 1

> Username: mloskot  
> Created at: 2019-12-13 19:28:53 UTC  
> Updated at: 2019-12-13 19:29:09 UTC  
> Url: https://github.com/boostorg/gil/issues/408#issuecomment-565576835  

Thanks for the report. I can confirm the issue while compiling for 32-bit target.  
  
FYI, the `old.hpp` should be considered as deprecated.  
  
  
p.s. Please consider submitting compilable MWE next time. The correct program that I am able to compile is:   
  
```cpp  
#include <boost/gil.hpp>  
#include <boost/gil/extension/io/png/old.hpp>  
int main()  
{  
    boost::gil::png_read_dimensions("abc.png");  
}  
```
