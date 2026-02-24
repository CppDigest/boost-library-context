# #95 - C++20: ‘class std::allocator’ has no member named ‘construct’ [Closed]

> Username: mclow  
> Created at: 2020-08-03 14:00:40 UTC  
> Updated at: 2021-10-27 07:45:19 UTC  
> Closed at: 2020-08-03 14:02:07 UTC  
> Url: https://github.com/boostorg/ublas/issues/95  

Taken from the -developers mailing list.  
  
Problem description:  
When compiling the following example:  
```  
#include <boost/numeric/ublas/matrix.hpp>  
#include <boost/numeric/ublas/io.hpp>  
  
int main () {  
using namespace boost::numeric::ublas;  
matrix m (3, 3);  
for (unsigned i = 0; i < m.size1 (); ++ i)  
  for (unsigned j = 0; j < m.size2 (); ++ j)  
    m (i, j) = 3 * i + j;  
std::cout << m << std::endl;  
}  
```  
  
with gcc-10 with -std=gnu++2a flag, compilation breaks  
Running the same compilation line with -std=gnu++17 passes  
  
boost build command lines:  
./bootstrap.sh --prefix="${PWD}/../boost-bin"  
./b2  
./b2 install  
  
Compiled on ubuntu 18.04, added gcc-10:  
sudo add-apt-repository ppa:ubuntu-toolchain-r/test  
sudo apt-get update  
sudo apt install gcc-10 g++-10  
  
Compilation error:  
test_boost.cpp:9:27: required from here  
/home/ubuntu/repos/cv-engine/externals/boost-bin/include/boost/numeric/ublas/storage.hpp:79:30: error: ‘class std::allocator’ has no member named ‘construct’  
79 | alloc_.construct(d, value_type());

---

## Comment 1

> Username: mclow  
> Created at: 2020-08-03 14:02:07 UTC  
> Url: https://github.com/boostorg/ublas/issues/95#issuecomment-668039945  

Duplicate. Sorry for the noise.

---

## Comment 2

> Username: jmarrec  
> Created at: 2021-10-27 07:45:18 UTC  
> Url: https://github.com/boostorg/ublas/issues/95#issuecomment-952629329  

The duplicate is https://github.com/boostorg/ublas/issues/96
