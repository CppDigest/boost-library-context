# #167 - Invalid string stored in vector [Closed]

> Username: DominikSadko  
> Created at: 2022-01-26 12:40:52 UTC  
> Updated at: 2022-03-06 21:40:34 UTC  
> Closed at: 2022-03-06 21:40:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/167  

Why string stored in variable `vectorOfStrings` at third place received as `"bbbb\xFF\xFF\xFF\xC0"` is not presented as value `"bbbb"` use in `push_back` method?  
  
Boost version: `1.78`  
  
Required system: `GNU/linux` (any distribution, tested on ubuntu)  
  
Compile command: `clang++ main.cpp -g -lrt -pthread -o test` (or use g++)  
  
Code:  
``` c++  
#include <bits/char_traits.h>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <boost/interprocess/containers/vector.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <cstring>  
  
namespace Interprocess = boost::interprocess;  
template <class T>  
using ShmAllocator = Interprocess::allocator<T, Interprocess::managed_shared_memory::segment_manager>;  
using ShmString = Interprocess::basic_string<char, std::char_traits<char>, ShmAllocator<char>>;  
  
  
int main()  
{  
    const auto memoryName = "test_memory";  
    Interprocess::shared_memory_object::remove(memoryName);  
    auto shm = Interprocess::managed_shared_memory(Interprocess::create_only, memoryName, 1024 * 1024);  
  
    auto stringAllocator = ShmAllocator<ShmString>(shm.get_segment_manager());  
    auto intAllocator = ShmAllocator<int>(shm.get_segment_manager());  
  
    auto vectorOfStrings = Interprocess::vector<ShmString, ShmAllocator<ShmString>>(stringAllocator);  
    auto vectorOfInts = Interprocess::vector<int, ShmAllocator<int>>(intAllocator);  
  
    {  
        const auto z = ShmString("aaaaaaaa", stringAllocator);  
        vectorOfStrings.push_back(z);  
        vectorOfInts.push_back(7);  
    }  
    {  
        const auto z = ShmString("ccccccccccccccccccccccc", stringAllocator);  
        vectorOfStrings.push_back(z);  
    }  
    {  
        const auto z = ShmString("bbbb", stringAllocator);  
        vectorOfStrings.push_back(z);  
    }  
  
    assert(std::strcmp(vectorOfStrings.at(0).c_str(), "aaaaaaaa") == 0);  
    assert(std::strcmp(vectorOfStrings.at(1).c_str(), "ccccccccccccccccccccccc") == 0);  
    assert(std::strcmp(vectorOfStrings.at(2).c_str(), "bbbb\xFF\xFF\xFF\xC0") == 0); // value received  
    assert(std::strcmp(vectorOfStrings.at(2).c_str(), "bbbb") == 0); // value expected  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-03-06 21:39:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/167#issuecomment-1060044252  

Thanks for the report, it was a bug in Boost.Container's string. I'll add a test-case in Interprocess to check it.
