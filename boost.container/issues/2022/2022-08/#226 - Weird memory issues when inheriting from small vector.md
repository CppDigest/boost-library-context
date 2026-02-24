# #226 - Weird memory issues when inheriting from small vector [Closed]

> Username: stertingen  
> Created at: 2022-08-09 12:16:34 UTC  
> Updated at: 2022-09-01 11:21:56 UTC  
> Closed at: 2022-09-01 11:21:55 UTC  
> Url: https://github.com/boostorg/container/issues/226  

Consider the following snippet: (https://godbolt.org/z/ax7TqGWTz)  
```c++  
#include <boost/container/small_vector.hpp>  
#include <cstdint>  
#include <iostream>  
#include <iomanip>  
  
// inspired by can_frame  
struct frame {  
    std::uint32_t header;  
    std::uint8_t len;  
    std::uint8_t data[8];  
};  
  
// Contains data from multiple frames  
struct S : boost::container::small_vector<std::uint8_t, 8> {  
    S() : my_header(0) {}  
  
    S(const frame& f)  
        : boost::container::small_vector<std::uint8_t, 8>(f.data, f.data + f.len),  
          my_header(f.header) {}  
  
    std::uint32_t my_header;  
};  
  
int main() {  
    frame f1;  
    f1.header = 0xAAAAAAAA;  
    f1.len = 8; // 8 bytes of data are filled  
    f1.data[0] = 0x01;  
    f1.data[1] = 0x23;  
    f1.data[2] = 0x45;  
    f1.data[3] = 0x67;  
    f1.data[4] = 0x89;  
    f1.data[5] = 0xAB;  
    f1.data[6] = 0xCD;  
    f1.data[7] = 0xEF;  
  
    S s(f1); // convert frame into S  
  
    std::cout << std::hex << "header: " << s.my_header << "\n";  
    for (std::uint8_t c : s) {  
        std::cout << unsigned(c) << " ";  
    }  
}  
```  
  
I do not expect payload data to be overwritten by the header.  
  
When having small_vector as data member instead of base class, the issue does not occur.  
  
Is it illegal to derive from small_vector?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-08-14 22:53:24 UTC  
> Url: https://github.com/boostorg/container/issues/226#issuecomment-1214464650  

Hi,  
  
The existing implementation was UB. Compilers can use the unused alignment bits in the base class to store new members. This was reported as a bug (https://github.com/boostorg/container/issues/218) and the implementation changed with the newly released Boost 1.80. Could you please try your code with Boost 1.80?

---

## Comment 2

> Username: stertingen  
> Created at: 2022-09-01 11:21:55 UTC  
> Url: https://github.com/boostorg/container/issues/226#issuecomment-1234140442  

Looks good: https://godbolt.org/z/Mx3cz1GnT (Showing Boost 1.79 vs. Boost 1.80)
