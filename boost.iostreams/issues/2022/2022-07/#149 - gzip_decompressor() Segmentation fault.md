# #149 - gzip_decompressor() Segmentation fault [Open]

> Username: woojae-jang  
> Created at: 2022-07-20 05:43:39 UTC  
> Updated at: 2024-07-03 13:07:05 UTC  
> Url: https://github.com/boostorg/iostreams/issues/149  

Hi  
  
Using the boost library, I was trying to read the gzip file. But i faced the Segmentation fault error.  
  
```cpp  
#include <boost/filesystem.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <boost/iostreams/filtering_streambuf.hpp>  
#include <fstream>  
#include <iostream>  
#include <string>  
  
using namespace std;  
using namespace boost::filesystem;  
  
int main(int argc, char** argv)  
{  
    boost::iostreams::filtering_streambuf<boost::iostreams::input> inbuf;  
    inbuf.push(boost::iostreams::gzip_decompressor());  
  
    return 0;  
}  
```  
  
> Segmentation fault (core dumped)  
  
> ==8215==The signal is caused by a READ memory access. #0 0x56360f5f70c8 in boost::iostreams::detail::gzip_header::reset() (/root/build/bin/test+0x900c8) #1 0x56360f5a14a4 in boost::iostreams::detail::gzip_header::gzip_header() /root/.conan/data/boost/1.79.0///package/adf48b8e4446dcb68f440fe42ff08878d5c7feed/include/boost/iostreams/filter/gzip.hpp:327 #2 0x56360f5a29c0 in boost::iostreams::basic_gzip_decompressorstd::allocator<char >::basic_gzip_decompressor(int, long) /root/.conan/data/boost/1.79.0///package/adf48b8e4446dcb68f440fe42ff08878d5c7feed/include/boost/iostreams/filter/gzip.hpp:736 #3 0x56360f59c091 in main /root/playground/main.cpp:14 #4 0x7f3063f530b2 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x240b2) #5 0x56360f59bedd in _start (/root/build/bin/test+0x34edd)  
  
**i used boost 1.79.0 with conan on Ubuntu 20.04.3 LTS**  
  
  
I have posted the same question on [stackoverflow](https://stackoverflow.com/questions/73043573/boost-gzip-filter-segmentation-fault-core-dumped-error).

---

## Comment 1

> Username: mclow  
> Created at: 2022-07-21 00:54:21 UTC  
> Url: https://github.com/boostorg/iostreams/issues/149#issuecomment-1190916043  

Another data point: I tried your program with boost trunk on Mac OS 10.15.7, and it ran w/o any problem.

---

## Comment 2

> Username: kmalinau  
> Created at: 2024-07-03 13:07:04 UTC  
> Url: https://github.com/boostorg/iostreams/issues/149#issuecomment-2206037422  

I am facing the same issue with boost 1.81.0. Did you finally resolve it?
