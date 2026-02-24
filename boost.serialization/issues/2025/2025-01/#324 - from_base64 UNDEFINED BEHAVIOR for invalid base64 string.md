# #324 - from_base64 UNDEFINED BEHAVIOR for invalid base64 string [Open]

> Username: ROCKFAL1  
> Created at: 2025-01-28 14:50:26 UTC  
> Updated at: 2025-03-14 06:56:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/324  

Compiler: MSVC  
boost version: 1.86.0  
  
For any string with `size() % 4 == 1` `from_base64` function increments past-of-end iterator  
Is this behavior expected? If this behavior is expected, then why doesn't the library provide functions for base64 validation?  
  
MRE:  
```  
#include <string>  
#include <vector>  
#include <algorithm>  
#include <iterator>  
  
#include <boost/archive/iterators/base64_from_binary.hpp>  
#include <boost/archive/iterators/binary_from_base64.hpp>  
#include <boost/archive/iterators/transform_width.hpp>  
  
template <typename OutIt>  
OutIt from_base64(const char* pstr, size_t size, OutIt out)  
{  
  using namespace boost::archive::iterators;  
  
  std::vector<char> buffer; // Just for debugging   
  std::copy_n(pstr, size, std::back_inserter(buffer));  
  
  using It = transform_width<binary_from_base64<std::vector<char>::iterator>, 8, 6>;  
  return  std::copy(It(buffer.begin()), It(buffer.end()), out);  
}  
```  
  
  
```  
  
const std::string base64str= "MTIzN"; // base64("1234567890") == "MTIzNDU2Nzg5MA=="  
std::string result;  
from_base64(base64str.c_str(), base64str.size(), std::back_inserter(result));  
 // Here i'm expecting smth like `result == "123"`, or `from_base64` throw exception (that i can wrap and return only valid part - "123")  
```
