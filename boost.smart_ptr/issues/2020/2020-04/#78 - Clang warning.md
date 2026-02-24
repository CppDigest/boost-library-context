# #78 - Clang warning [Closed]

> Username: DarkerStar  
> Created at: 2020-04-26 14:52:32 UTC  
> Updated at: 2020-04-28 21:51:05 UTC  
> Closed at: 2020-04-28 21:51:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78  

(Sorry, accidentally hit wrong key and posted this while still writing - in fact while writing the title.)  
  
Clang 11 generates a warning when compiling with just "`-pedantic`":  
  
```  
$ clang++ -pedantic -I/mnt/saturn/source/boost_1_72_0 -c -o main.o main.cpp  
In file included from main.cpp:1:  
In file included from /mnt/saturn/source/boost_1_72_0/boost/smart_ptr.hpp:19:  
In file included from /mnt/saturn/source/boost_1_72_0/boost/shared_ptr.hpp:17:  
In file included from /mnt/saturn/source/boost_1_72_0/boost/smart_ptr/shared_ptr.hpp:28:  
In file included from /mnt/saturn/source/boost_1_72_0/boost/smart_ptr/detail/shared_count.hpp:29:  
In file included from /mnt/saturn/source/boost_1_72_0/boost/smart_ptr/detail/sp_counted_base.hpp:45:  
/mnt/saturn/source/boost_1_72_0/boost/smart_ptr/detail/sp_counted_base_clang.hpp:29:9: warning: '_Atomic' is a C11 extension [-Wc11-extensions]  
typedef _Atomic( boost::int_least32_t ) atomic_int_least32_t;  
        ^  
1 warning generated.  
$ cat main.cpp  
#include <boost/smart_ptr.hpp>  
  
int main() {}  
$ clang++ --version  
clang version 11.0.0-++20200414060218+d1a677cd33e-1~exp1~20200414040817.489   
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
```  
  
(Boost 1.72.0 source acquired by downloading the tar.gz from the boost.org site.)

---

## Comment 1

> Username: pdimov  
> Created at: 2020-04-26 15:10:39 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-619567354  

Where would we be without pedantry :-)

---

## Comment 2

> Username: pdimov  
> Created at: 2020-04-26 15:12:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-619567585  

Is this warning new in Clang 11, or do earlier versions emit it too?

---

## Comment 3

> Username: DarkerStar  
> Created at: 2020-04-26 15:15:09 UTC  
> Updated at: 2020-04-26 15:16:07 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-619567980  

I only have 7, 8, and 9 installed, and Wandbox doesn't seem to support Boost with 10. It *looks* new in 11.  
  
Edit: Godbolt has 10 + Boost. No warning there.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-04-26 15:51:37 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-619573688  

Godbolt uses -isystem, so it doesn't emit warnings from Boost.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-04-26 15:53:30 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-619574008  

Seems that Clang 10 also has it: https://godbolt.org/z/r2VGme

---

## Comment 6

> Username: pdimov  
> Created at: 2020-04-28 11:44:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-620554648  

Should be fixed on develop by https://github.com/boostorg/smart_ptr/commit/066b398114ab48c81c875e95459c15c2416a7891.

---

## Comment 7

> Username: DarkerStar  
> Created at: 2020-04-28 21:51:05 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/78#issuecomment-620874229  

Yup, warning's gone!
