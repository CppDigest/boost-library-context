# #84 - Leaf allows C-style string as error objects [Closed]

> Username: ericli-splunk  
> Created at: 2025-10-07 00:34:28 UTC  
> Updated at: 2025-10-13 21:15:39 UTC  
> Closed at: 2025-10-13 21:15:39 UTC  
> Url: https://github.com/boostorg/leaf/issues/84  

### Version of Boost  
  
`1.83.0`  
  
### Actual and Expected Behavior  
  
When running the following program  
  
```cpp  
#include <string>  
#include <iostream>  
#include <boost/leaf/result.hpp>  
#include <boost/leaf/handle_errors.hpp>  
  
using namespace boost;  
  
int main () {  
    leaf::try_handle_all(  
        []() -> leaf::result<void> {  
            boost::leaf::throw_exception("foo");  
        },  
        [](std::string err) {  
            std::cout << "std::string: " << err << std::endl;  
        },  
        [](const char* err) {  
            std::cout << "const char*: " << (void *)err << std::endl;  
        },  
        []() {  
            std::cout << "general" << std::endl;  
        }  
    );  
    return 0;  
}  
```  
  
Actual output:  
```  
const char*: 0  
```  
  
Expected Behavior:  
* Compile error on line `boost::leaf::throw_exception("foo");` saying something like "Error objects of pointer types are not allowed"  
  
#### Explanation  
  
Some people may expect the program to output "std::string: foo" or "const char*: 0x12345678". However, this is not the case because Leaf does not support pointer type error objects. Thus, I think my code should not compile.  
  
It appears that the following code is designed to prevent my code from compiling https://github.com/boostorg/leaf/blob/61e4b0db4e1934f91a06d6f0cbbe8d5f91f96383/include/boost/leaf/error.hpp#L432  
  
However, my code compiles fine because `e`'s type is `const char (&)[4]`, not `const char *`. The latter is a pointer type, but the former is not.  
  
### Steps necessary to reproduce the problem  
  
Compile and run using `g++ a.cpp -o a -g && ./a`  
  
### All relevant compiler information  
  
`g++ (Debian 14.2.0-19) 14.2.0`

---

## Comment 1

> Username: zajo  
> Created at: 2025-10-07 02:33:36 UTC  
> Url: https://github.com/boostorg/leaf/issues/84#issuecomment-3374945758  

Thanks, this is a good catch.

---

## Comment 2

> Username: ericli-splunk  
> Created at: 2025-10-07 16:53:02 UTC  
> Url: https://github.com/boostorg/leaf/issues/84#issuecomment-3377739053  

I think https://github.com/boostorg/leaf/pull/85 will fix this issue.

---

## Comment 3

> Username: zajo  
> Created at: 2025-10-08 03:20:50 UTC  
> Url: https://github.com/boostorg/leaf/issues/84#issuecomment-3379427931  

We should add a test also. I'll look into this in the next weekend.
