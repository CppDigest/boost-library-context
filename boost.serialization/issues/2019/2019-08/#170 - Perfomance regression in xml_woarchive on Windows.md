# #170 - Perfomance regression in xml_woarchive on Windows [Closed]

> Username: AlexVlk  
> Created at: 2019-08-20 14:42:39 UTC  
> Updated at: 2019-10-19 16:08:36 UTC  
> Closed at: 2019-10-19 16:08:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/170  

I've found perfomance regression in xml_woarchive after boost updating from 1.60 to 1.70. Compiler version MSVC 14.0. The problem can be noticed on huge collection serialization. Example:  
  
```c++  
#include <iostream>  
#include <sstream>  
#include <chrono>  
  
#include <boost/archive/xml_woarchive.hpp>  
#include <boost/serialization/vector.hpp>  
  
int main()  
{  
  std::vector<int> data(1000000, 0);  
  
  std::wstringstream oss;  
  {  
    boost::archive::xml_woarchive oa(oss);  
  
    auto start = std::chrono::system_clock::now();  
  
    oa << BOOST_SERIALIZATION_NVP(data);  
  
    auto end = std::chrono::system_clock::now();  
  
    std::chrono::duration<double> diff = end - start;  
    std::cout << "Time to serialize vector: " << diff.count() << " s" << std::endl;  
  }  
     
  oss.flush();  
  std::cout << "Result string size: " << oss.str().size() / 0x100000 << " Mbyte" << std::endl;  
  
  return 0;  
}  
```  
  
Examples output with boost 1.60:  
Time to serialize vector: 1.42714 s  
Result string size: 15 Mbyte  
  
Examples output with boost 1.70:  
Time to serialize vector: 106.599 s  
Result string size: 15 Mbyte  
  
Profiling shows that 95% of work is located in std::codecvt ctor, wich is called from boost::archive::detail::utf8_codecvt_facet::utf8_codecvt_facet.

---

## Comment 1

> Username: robertramey  
> Created at: 2019-10-19 16:08:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/170#issuecomment-544165879  

Hmmm - I repeated your test on my machine.  I'm using mac Xcode and compiling in debug mode.  This is significantly slower than release mode.  The Xcode IDE uses clang its compiler.  The execution output shows:  
  
>Time to serialize vector: 3.37749 s  
Result string size: 15 Mbyte  
Program ended with exit code: 0  
  
The execution time of 3.37 s seems comparable to the 1.60 result you cite above.  
  
So I can't do much more unless you want to track this down to an actual code change suggesting.  That is, if you want action here, I'll need some more help.  I'm going to close this.  Feel free to re-open it if you get more information.
