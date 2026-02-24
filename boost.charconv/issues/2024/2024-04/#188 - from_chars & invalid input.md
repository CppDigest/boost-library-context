# #188 - from_chars & invalid input [Closed]

> Username: oknenavin  
> Created at: 2024-04-22 07:30:02 UTC  
> Updated at: 2024-04-23 06:18:00 UTC  
> Closed at: 2024-04-23 06:18:00 UTC  
> Url: https://github.com/boostorg/charconv/issues/188  

Hi,  
  
The code below fails. 1.85, ubuntu 22.04, g++-13.  
``` c++  
#include <charconv>  
#include <boost/charconv.hpp>  
  
int main() {  
    {   auto i = "x"; int o;  
        auto r = std::from_chars(i, i + 1, o);  
        assert(r.ec == std::errc::invalid_argument);  
    }  
    {   auto i = "x"; int o;  
        auto r = boost::charconv::from_chars(i, i + 1, o);  
        assert(r.ec == std::errc::invalid_argument);  
    }  
}  
```  
According to cppreference:  
*If there is no pattern match, returns a value of type std::from_chars_result such that ptr equals first and ec equals std::errc::invalid_argument. value is unmodified.*  
Am I reading it wrong?  
  
Regards

---

## Comment 1

> Username: mborland  
> Created at: 2024-04-22 07:38:21 UTC  
> Url: https://github.com/boostorg/charconv/issues/188#issuecomment-2068696670  

You're reading that right. I'll take a look.
