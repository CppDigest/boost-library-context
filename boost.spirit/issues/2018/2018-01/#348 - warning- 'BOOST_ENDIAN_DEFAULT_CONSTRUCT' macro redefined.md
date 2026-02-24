# #348 - warning: 'BOOST_ENDIAN_DEFAULT_CONSTRUCT' macro redefined [Closed]

> Username: redboltz  
> Created at: 2018-01-04 00:50:23 UTC  
> Updated at: 2019-03-07 23:14:13 UTC  
> Closed at: 2019-03-07 23:14:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/348  

When I include both `boost/spirit/include/qi.hpp` and `boost/endian/buffers.hpp`, I got `warning: 'BOOST_ENDIAN_DEFAULT_CONSTRUCT' macro redefined`.  
  
```cpp  
#include <boost/spirit/include/qi.hpp>  
#include <boost/endian/buffers.hpp>  
  
int main() {  
}  
```  
  
https://wandbox.org/permlink/lwc8uW2Giwr3CWO7  
  
It seems that the macro should be defined appropriate point, but I'm not sure where is it.  
  
I found the issue Boost 1.66.0 (current release).

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-01-04 19:41:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/348#issuecomment-355378960  

Should be fixed in #349.
