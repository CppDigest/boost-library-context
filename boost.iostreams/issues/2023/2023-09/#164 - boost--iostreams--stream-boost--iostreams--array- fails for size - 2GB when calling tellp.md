# #164 - boost::iostreams::stream<boost::iostreams::array> fails for size > 2GB when calling tellp [Open]

> Username: KrisThielemans  
> Created at: 2023-09-03 19:50:35 UTC  
> Updated at: 2023-09-06 04:50:37 UTC  
> Url: https://github.com/boostorg/iostreams/issues/164  

Test program:  
```c++  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/stream.hpp>  
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
  typedef boost::iostreams::stream<boost::iostreams::array> array_stream;  
  array_stream s(reinterpret_cast<char *>(buffer.data()), buf_size);  
  
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
fails. g++-8 and boost 1.65.1 gives  
```  
...  
before (1704) 2145322368  
before (1705) 2146581360  
before (1706) -1  
before (1707) -1  
...  
wrong output  
```  
g++-12 and boost 1.74.0. gives  
```  
...  
before (1704) 2145322368  
before (1705) 2146581360  
terminate called after throwing an instance of 'boost::wrapexcept<std::ios_base::failure[abi:cxx11]>'  
  what():  bad seek: iostream error  
Aborted (core dumped)  
```  
It works fine when not calling `tellg()`. This is similar to https://github.com/boostorg/interprocess/issues/190, although here we don't have a segfault.

---

## Comment 1

> Username: mclow  
> Created at: 2023-09-03 20:03:30 UTC  
> Url: https://github.com/boostorg/iostreams/issues/164#issuecomment-1704389499  

I'm not seeing a call to `tellg` in your example.

---

## Comment 2

> Username: KrisThielemans  
> Created at: 2023-09-03 20:09:54 UTC  
> Url: https://github.com/boostorg/iostreams/issues/164#issuecomment-1704390939  

sorry! I meant `tellp`. I haven't tried with `tellg` yet.

---

## Comment 3

> Username: mclow  
> Created at: 2023-09-03 21:25:22 UTC  
> Url: https://github.com/boostorg/iostreams/issues/164#issuecomment-1704407919  

`std::ios_base::failure` is what gets thrown when a seek fails.  
  
This suggests that somewhere (maybe in boost, maybe in the underlying standard library) there is a problem with seeking past 0x7FFFFFFFF.

---

## Comment 4

> Username: KrisThielemans  
> Created at: 2023-09-03 21:35:35 UTC  
> Url: https://github.com/boostorg/iostreams/issues/164#issuecomment-1704410516  

It is in boost. I've tried it with `std::stringstream` and `std::fstream`

---

## Comment 5

> Username: mclow  
> Created at: 2023-09-06 04:50:37 UTC  
> Url: https://github.com/boostorg/iostreams/issues/164#issuecomment-1707653051  

I have reproduced it; and it is throwing from:  
```  
  * frame #0: 0x000000010000343c a.out`boost::iostreams::detail::direct_streambuf<boost::iostreams::basic_array<char>, std::__1::char_traits<char> >::seek_impl(this=0x00007ff7bfeff418, off=0, way=cur, which=16) at direct_streambuf.hpp:238:36  
```  
https://github.com/boostorg/iostreams/blob/055c1cc7599e8e30d4339994617d4ee36f3cdc3d/include/boost/iostreams/detail/streambuf/direct_streambuf.hpp#L238
