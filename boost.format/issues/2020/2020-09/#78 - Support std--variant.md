# #78 - Support std::variant [Open]

> Username: heretic13  
> Created at: 2020-09-11 09:22:07 UTC  
> Updated at: 2024-05-07 16:10:42 UTC  
> Url: https://github.com/boostorg/format/issues/78  

Hello. I have a problem with using std::variant with boost::format.  
Visual Studio 2019 - 16.7.3  
boost - 1.74  
  
Sample code:  
  
`  
#include < iostream>  
#include < variant>  
#include <boost/variant.hpp>  
#include <boost/format.hpp>  
  
typedef std::variant<uint16_t, std::string>	MemCaptureIdType;  
//typedef boost::variant<uint16_t, std::string>	MemCaptureIdType;  
  
std::ostream& operator<<(std::ostream& os, const MemCaptureIdType& type)  
{  
    return os;  
}  
  
int main()  
{  
	auto tmp1 = MemCaptureIdType(0);  
  
	auto tmp = boost::format("%1%") % tmp1;  
  
	return 0;  
}  
`  
  
But it works when I use boost::variant.  
I think we need to support std::variant.

---

## Comment 1

> Username: xiongyang  
> Created at: 2024-05-07 15:39:56 UTC  
> Updated at: 2024-05-07 16:10:42 UTC  
> Url: https://github.com/boostorg/format/issues/78#issuecomment-2098757003  

I meet the same problem with std::optional.  
  
  
gcc (GCC) 13.1.1 20230614 (Red Hat 13.1.1-4)  
boost-1.84  
  
here is the code  
  
```  
#include <iostream>  
#include <string>  
#include <optional>  
#include "boost/format.hpp"  
  
template <typename StreamType>  
StreamType& operator<<(StreamType& str, const std::optional<double>& id)  
{  
    return str;  
}  
int main()  
{  
        std::optional<double> xxx;  
        std::cout << xxx;  
        boost::format("%1%") % xxx;  
        return 0;  
};  
  
```
