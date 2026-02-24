# #245 - Whitespace parser bp::ws seems to be extremely slow [Closed]

> Username: andreasbuhr  
> Created at: 2025-07-02 14:49:28 UTC  
> Updated at: 2025-10-14 14:26:13 UTC  
> Closed at: 2025-10-13 23:21:48 UTC  
> Url: https://github.com/boostorg/parser/issues/245  

Hi, I am trying boost.parser. Thanks a lot for creating it!  
  
I am trying some almost trivial examples and stumbled over the fact that even a very simple parser allocates and deallocates a lot in the skip functions.   
  
An example:   
  
Program runtime is 5.6 seconds.  
Out of this is 2.1 seconds in malloc_base, in construction of the two objects `symbol_table_tries` and `pending_symbol_table_operations` in https://github.com/boostorg/parser/blob/5788fb6967fb39db76bc98b415b3fa7776a9ca5e/include/boost/parser/parser.hpp#L1522  
Another 2.0 seconds is in the destructors of these two objects.  
  
75% of the parse time is construction and destruction of these two objects.  
I am using MSVC 2022.  
  
It would be great if this could be improved. If I can help, let me know.  
  
Reproducer:  
```  
#include <boost/parser/parser.hpp>  
#include <chrono>  
#include <string>  
  
  
namespace bp = boost::parser;  
  
int main()  
{  
    std::u8string teststring = u8" 1, 2, 3";  
    for (int i = 0; i < 10000000; ++i) teststring += u8", 4, 3, 2";  
  
    auto starttime = std::chrono::high_resolution_clock::now();  
    const auto result = bp::parse(teststring, bp::int_ % ',', bp::ws);  
    auto endtime = std::chrono::high_resolution_clock::now();  
    double duration = std::chrono::duration_cast<std::chrono::duration<double>>(endtime - starttime).count();  
    std::cout << duration << std::endl;  
}  
```  
  
Screenshot of VTune:  
  
![Image](https://github.com/user-attachments/assets/48a9d047-467a-45e0-9fcb-29c3b4352646)  
  
Callstack of malloc_base:  
  
![Image](https://github.com/user-attachments/assets/f5c8616a-ac84-4f02-8b26-1ce5b2b3ffba)

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-07-04 07:43:04 UTC  
> Updated at: 2025-07-04 07:43:28 UTC  
> Url: https://github.com/boostorg/parser/issues/245#issuecomment-3034855711  

Explicitly integrating the whitespaceparser reduces the parse time to about 1/3.  
  
The following program runs approximately 1.9 seconds on my machine, vs. 5.6 seconds of the program above.  
  
```  
#include <boost/parser/parser.hpp>  
#include <chrono>  
#include <string>  
  
  
namespace bp = boost::parser;  
  
int main()  
{  
    std::u8string teststring = u8" 1, 2, 3";  
    for (int i = 0; i < 10000000; ++i) teststring += u8", 4, 3, 2";  
  
    auto starttime = std::chrono::high_resolution_clock::now();  
    const auto result = bp::parse(teststring, *bp::ws >> bp::int_ >> *(*bp::ws >> ',' > *bp::ws > bp::int_) >> *bp::ws);  
    auto endtime = std::chrono::high_resolution_clock::now();  
    double duration = std::chrono::duration_cast<std::chrono::duration<double>>(endtime - starttime).count();  
    std::cout << duration << std::endl;  
}  
```

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-10-13 23:21:50 UTC  
> Url: https://github.com/boostorg/parser/issues/245#issuecomment-3399378849  

Thanks for pointing this out!  Please check out 1f5303c756df98110407523deea212feab1b45c6, and see if it fixes things for you.

---

## Comment 3

> Username: andreasbuhr  
> Created at: 2025-10-14 14:26:13 UTC  
> Url: https://github.com/boostorg/parser/issues/245#issuecomment-3402151671  

Yes, this solves the problem. Thanks a lot.
