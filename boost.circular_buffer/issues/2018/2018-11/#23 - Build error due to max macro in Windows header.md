# #23 - Build error due to max macro in Windows header [Closed]

> Username: MaartenBent  
> Created at: 2018-11-28 21:10:31 UTC  
> Updated at: 2018-11-29 01:24:49 UTC  
> Closed at: 2018-11-29 01:24:48 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/23  

With Boost 1.69.0 beta 1 compilation of an application that uses a circular buffer fails with the following error:  
```boost/circular_buffer/allocators.hpp(38): error C2589: '(': illegal token on right side of '::'```  
because `max` is defined as a macro in `Windows.h`.  
  
This can be fixed by preventing macro expansion by adding parentheses:  
```return (std::numeric_limits<size_type>::max)() / sizeof(value_type);```  
  
Example code:  
```c++  
#include <windows.h>  
#include <boost/circular_buffer.hpp>  
  
int main(int /*argc*/, char* /*argv*/[])  
{  
	boost::circular_buffer<int> cb(3);  
	return 0;  
}  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2018-11-29 01:24:48 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/23#issuecomment-442670144  

Windows header macros strike again. Thanks for reporting this before the 1.69 release; fixed in b07fbd0.
