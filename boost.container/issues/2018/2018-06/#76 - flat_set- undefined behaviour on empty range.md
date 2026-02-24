# #76 - flat_set: undefined behaviour on empty range [Closed]

> Username: gjasny  
> Created at: 2018-06-27 12:33:21 UTC  
> Updated at: 2018-06-28 07:45:05 UTC  
> Closed at: 2018-06-27 21:32:10 UTC  
> Url: https://github.com/boostorg/container/issues/76  

Hello,  
  
the following code triggers the undefined behaviour sanitiser:  
  
```  
#include <cstdint>  
#include <boost/container/flat_set.hpp>  
  
using PacketSet = boost::container::flat_set<std::uint32_t>;  
  
int main(int argc, const char * argv[]) {  
  
    const PacketSet empty;  
    PacketSet target;  
    target.insert(empty.begin(), empty.end());  
  
    return 0;  
}  
```  
  
Output is:  
```  
boost/V1.67.0_6/boost/container/vector.hpp:125:14: runtime error: reference binding to null pointer of type 'unsigned int'  
boost/V1.67.0_6/boost/container/vector.hpp:128:76: runtime error: reference binding to null pointer of type 'unsigned int'  
boost/V1.67.0_6/boost/intrusive/pointer_traits.hpp:292:49: runtime error: reference binding to null pointer of type 'unsigned int'  
boost/V1.67.0_6/boost/move/detail/meta_utils.hpp:270:49: runtime error: reference binding to null pointer of type 'unsigned int'  
boost/V1.67.0_6/boost/move/detail/meta_utils.hpp:246:55: runtime error: reference binding to null pointer of type 'unsigned int'  
boost/V1.67.0_6/boost/move/detail/meta_utils.hpp:247:57: runtime error: reference binding to null pointer of type 'unsigned int'  
boost/V1.67.0_6/boost/move/detail/meta_utils.hpp:270:9: runtime error: reference binding to null pointer of type 'unsigned int'  
```  
  
Thanks,  
Gregor

---

## Comment 1

> Username: gjasny  
> Created at: 2018-06-27 13:06:22 UTC  
> Url: https://github.com/boostorg/container/issues/76#issuecomment-400664607  

@igaztanaga: I don't know if you get notified automatically.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-06-27 21:32:10 UTC  
> Url: https://github.com/boostorg/container/issues/76#issuecomment-400835914  

It seems that   
  
https://github.com/boostorg/container/commit/b56cbb60e05cc06fbebed03f8b359b116e788f2a  
  
fixes it (operator-> should not be based on operator*). Thanks for the report.

---

## Comment 3

> Username: gjasny  
> Created at: 2018-06-28 07:45:04 UTC  
> Url: https://github.com/boostorg/container/issues/76#issuecomment-400943700  

Thanks, verified.
