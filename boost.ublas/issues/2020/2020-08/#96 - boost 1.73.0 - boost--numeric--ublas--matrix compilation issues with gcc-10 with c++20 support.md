# #96 - boost 1.73.0 - boost::numeric::ublas::matrix compilation issues with gcc-10 with c++20 support [Closed]

> Username: eladedgy  
> Created at: 2020-08-03 10:14:37 UTC  
> Updated at: 2020-12-15 15:24:24 UTC  
> Closed at: 2020-08-12 06:26:14 UTC  
> Url: https://github.com/boostorg/ublas/issues/96  

Problem description:  
When compiling the following example:  
#include <boost/numeric/ublas/matrix.hpp>  
#include <boost/numeric/ublas/io.hpp>  
  
int main () {  
    using namespace boost::numeric::ublas;  
    matrix<double> m (3, 3);  
    for (unsigned i = 0; i < m.size1 (); ++ i)  
        for (unsigned j = 0; j < m.size2 (); ++ j)  
            m (i, j) = 3 * i + j;  
    std::cout << m << std::endl;  
}  
  
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
test_boost.cpp:9:27:   required from here  
boost-bin/include/boost/numeric/ublas/storage.hpp:79:30: error: ‘class std::allocator<double>’ has no member named ‘construct’  
   79 |                       alloc_.construct(d, value_type());

---

## Comment 1

> Username: bassoy  
> Created at: 2020-08-03 18:32:58 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-668176777  

Thanks @eladedgy for using boost.ublas.  
We are aware of this issue and we already included a bugfix for the upcoming release boost release.   
Please have a look at the already merged PR https://github.com/boostorg/ublas/pull/83.  
I will close this issue as the bug is fixed with commit  https://github.com/boostorg/ublas/commit/47a314dd01d1adacaaf7265c7c644f8f70afd402 on the development branch.

---

## Comment 2

> Username: mclow  
> Created at: 2020-08-04 00:53:40 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-668312600  

I see this commit in `develop`, but not in `master`.

---

## Comment 3

> Username: eladedgy  
> Created at: 2020-08-04 05:35:36 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-668389429  

Please notice I've tested it on 1.74 beta 1 as well as issue is reproducible on that branch

---

## Comment 4

> Username: bassoy  
> Created at: 2020-08-04 20:13:59 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-668802455  

> I see this commit in `develop`, but not in `master`.  
  
Yes. I am not sure if we can pull the commit into master as we are late for the 1.74 release which will be closed for merges in one day. We need two contributors to   
  
@mclow what is your advise?

---

## Comment 5

> Username: eladedgy  
> Created at: 2020-08-05 08:59:01 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-669073151  

Hi,  
gcc-10 is already released for a couple of months, and it's supported out-of-the-box in Ubuntu 20.04 LTS  
releasing 1.74.0 with this bug means no one will be able to compile with C++20 on those machines or Ubuntu 20.04 based containers  
This is not a minor issue which can be released

---

## Comment 6

> Username: jwakely  
> Created at: 2020-08-05 10:49:12 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-669123920  

Fedora 32 (released three months ago) also uses GCC 10.

---

## Comment 7

> Username: glenfe  
> Created at: 2020-08-12 06:26:14 UTC  
> Url: https://github.com/boostorg/ublas/issues/96#issuecomment-672635623  

Fixed in develop with #103.
