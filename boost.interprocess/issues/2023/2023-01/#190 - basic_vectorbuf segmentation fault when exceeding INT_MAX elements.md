# #190 - [vectorstream] basic_vectorbuf segmentation fault when exceeding INT_MAX elements [Closed]

> Username: grrtrr  
> Created at: 2023-01-07 17:56:20 UTC  
> Updated at: 2024-07-06 20:44:53 UTC  
> Closed at: 2024-07-06 20:44:53 UTC  
> Url: https://github.com/boostorg/interprocess/issues/190  

We observed this problem on `boost` 1.71, but it is also present in 1.81.  
  
### Problem description  
  
On a system with 4-byte `int` and an `INT_MAX` of 2147483647, attempting to store more than that in a `basic_vectorbuf` causes problems due to converting a _larger type_ ([`std::char_traits::off_type`](https://en.cppreference.com/w/cpp/string/char_traits) or [`std::vector::difference_type`](https://en.cppreference.com/w/cpp/container/vector)) to an `int`:  
- `std::streambuf::pbump` and `std::streambuf::gbump` both implicitly convert to `int`,  
- passing more than `INT_MAX` to these functions moves `pptr/gptr` _before_ `pbase/eback` - access causes segmentation fault;  
- there are a couple of other type casts to `int` which would also produce negative offsets due to overflow.  
  
### How to reproduce  
  
The problem is fully reproducible by  
- storing more than `INT_MAX` elements in a `basic_vectorbuf`, or  
- using `reserve()` to allocate more than `INT_MAX` elements, and then calling `seekoff` or `tellp()` once the write position has advanced past `INT_MAX`.

---

## Comment 1

> Username: KrisThielemans  
> Created at: 2023-09-03 18:48:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/190#issuecomment-1704373897  

The same problem exists for `bufferstream`. Test program:  
```c++  
#include <boost/interprocess/streams/bufferstream.hpp>  
#include <vector>  
#include <cstdlib>  
#include <iostream>  
#include <cmath>  
  
int main(int argc, char* argv[])  
{  
  // some numbers such that total buffer is larger than 2GB  
  const std::streamoff s1(2000);  
  const std::streamoff s2(314748);  
  std::vector<float> buffer(s1*s2);  
  const auto buf_size = static_cast<std::streamsize>(buffer.size())* sizeof(float);  
  boost::interprocess::bufferstream s(reinterpret_cast<char*>(buffer.data()), buf_size, std::ios::in | std::ios::out | std::ios::binary);  
  
  // write some data from a smaller buffer  
  std::vector<float> buffer2(s2, 1.F);  
  for (std::streamoff i=0; i<s1; ++i)  
    {  
      std::cout << "before (" << i << ") " << s.tellp() << std::endl;  
      s.write(reinterpret_cast<const char *>(buffer2.data()), s2*sizeof(float));  
    }  
  if (std::abs(buffer[s1*s2-1] -1.F) > .001F)  
    {  
      std::cerr << "wrong output\n";  
      return 1;  
    }  
  return 0;  
}  
```  
This segfaults with g++-8 and boost 1.65.1, and also g++-12 and boost 1.74.0.  
```  
...  
before (1703) 2144063376  
before (1704) 2145322368  
before (1705) 2146581360  
before (1706) 2147840352  
Segmentation fault (core dumped)  
```  
If you comment out the `tellg()`, then it runs fine.
