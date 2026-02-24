# #68 - operator-(const time_point &, const duration &) mishandles unsigned numbers [Open]

> Username: jlanca  
> Created at: 2023-01-25 09:16:11 UTC  
> Updated at: 2023-01-25 09:16:11 UTC  
> Url: https://github.com/boostorg/chrono/issues/68  

The binary subtraction operation `tp - d` is implemented as `tp + (-d)`.  
Because the unary minus operator is used, the result is wrong if the underlying representation type is unsigned.  
  
For instance :  
```  
#include <cassert>  
#include <chrono>  
#include <iostream>  
#include <boost/chrono.hpp>  
   
// namespace chrono = std::chrono;  
// using std::micro;  
namespace chrono = boost::chrono;  
using boost::micro;  
   
int main() {  
   
  const auto tp = chrono::system_clock::time_point(chrono::milliseconds(0));  
  chrono::duration<uint32_t, micro> delay(1);  
  std::cout << (tp - delay).time_since_epoch().count() << std::endl;  
  assert((tp - delay).time_since_epoch().count() == -1000);  
  return 0;  
}  
```   
Running this program produces the following output:  
```  
4294967295000  
a: /tmp/a.cpp:16: int main(): Assertion `(tp - delay).time_since_epoch().count() == -1000' failed.  
```  
  
  
Note that the std::chrono from gcc does not exhibit this defect.
