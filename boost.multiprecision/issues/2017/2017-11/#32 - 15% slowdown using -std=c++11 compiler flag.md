# #32 - 15% slowdown using -std=c++11 compiler flag [Closed]

> Username: kimwalisch  
> Created at: 2017-11-20 12:20:41 UTC  
> Updated at: 2018-01-02 15:32:03 UTC  
> Closed at: 2018-01-02 15:32:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32  

Hi,  
  
Unfortunately [my latest post](https://lists.boost.org/Archives/boost/2017/11/239899.php) to the boost mailing list is an unreadable mess, that's why I am reposting it here.  
  
> No, not known, and if anything C++11 should speed things up by enabling   
rvalue-references etc. If you can narrow it down some more I'll certainly   
investigate.   
  
OK, I'll try to narrow it down. The simplest algorithm using the ```int256_t``` type in primesum is [S2_trivial](https://github.com/kimwalisch/primesum/blob/256-bit/src/deleglise-rivat/S2_trivial.cpp#L32). Here is the inner loop of the algorithm:  
  
```C++  
while ((prime = iter.next_prime()) < stop)  
{  
    int64_t xn = (int64_t) max(x / (prime * prime), prime);  
    maxint_t diff = prime_sums[pi[y]] - prime_sums[pi[xn]];  
    s2_trivial += prime * diff;  
}  
```  
  
There are only 2 lines of code using the ```int256_t``` type in this algorithm:  
  
```C++  
maxint_t diff = prime_sums[pi[y]] - prime_sums[pi[xn]];  
s2_trivial += prime * diff;  
```  
  
Note that ```maxint_t``` is a typedef for ```int256_t```. The first line does an ```__int128_t``` substraction and converts the result (impliciltly) to ```int256_t```. The second code line does an ```int256_t``` multiplication and adds the result to the ```int256_t s2_trivial``` variable.  
  
As soon as I add ```-std=c++11``` to the compiler flags the algorithm runs 15% slower (using Clang and GCC on Linux x86_64). Funnily, if I change the code lines to:  
  
```C++  
maxint_t diff = prime_sums[pi[y]] - prime_sums[pi[xn]];  
maxint_t prime2 = prime;  
diff *= prime2;  
s2_trivial += diff;  
```  
  
This code runs already 11% faster using ```-std=c++11``` even though it does exactly the same (and only 4% slower than without -std=c++11). Without ```-std=c++11``` this code does not run faster. My code mixes ```__int128_t``` with ```int256_t``` a lot and one of my guesses on what causes the slowdown is that the ```__int128_t``` to ```int256_t``` conversion has become much slower (in some cases) using ```-std=c++11```.

---

## Comment 1

> Username: kimwalisch  
> Created at: 2017-11-21 12:25:21 UTC  
> Updated at: 2017-11-21 12:36:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-346011293  

I have now created a simple benchmark program which reproduces the performance regression when compiling using ```-std=c++11``` (and also using ```-std=c++14```):  
  
```C++  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
using namespace std;  
using namespace boost::multiprecision;  
  
int main()  
{  
    __int128_t i = 0;  
    __int128_t j = 0;  
  
    int256_t sum = 0;  
  
    for (; i < 1e9; i += 19, j += 13)  
    {  
        int256_t diff = j;  
        sum += i * diff;  
    }  
  
    std::cout << "Result: " << sum << std::endl;  
  
    return 0;  
}  
```  
  
Here are the results on my Ubuntu 16.04 x64 system with: GCC 5.4, Boost 1.65.1 and an Intel Skylake i7-6700 CPU:  
  
```bash  
$ g++ -O2 test.cpp -o test  
$ time ./test  
Result: 12003693138042476126038783  
  
real	0m0.771s  
user	0m0.768s  
sys	0m0.000s  
```  
  
And here using ```-std=c++11```:  
  
```bash  
$ g++ -std=c++11 -O2 test.cpp -o test  
$ time ./test  
Result: 12003693138042476126038783  
  
real	0m1.096s  
user	0m1.088s  
sys	0m0.004s  
```  
  
So by simply adding ```-std=c++11``` to the compiler flags the program runs 30% slower!

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-12-31 17:32:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354615800  

Investigating this gets interesting... or at least perplexing!  First off I am unable to reproduce on either cygwin or minw.  However, there was an issue with rvalue references that was niggling me: some of the operator overloads were incorrectly written from a performance point of view - they should have relied on the named return value optimization, but were returning via std::move instead.  That is now fixed in develop, but seems to make no difference to performance, perhaps because the functions in question get optimized out of existence anyway.  
  
So... firing up ubuntu, I am able to reproduce with gcc-5.4:  
  
```  
jzm@jzm:~/boost/boost$ g++ -I. -std=c++03  -O3  t.cpp && time ./a.out  
  
real	0m1.057s  
user	0m1.048s  
sys	0m0.000s  
jzm@jzm:~/boost/boost$ g++ -I. -std=c++11  -O3  t.cpp && time ./a.out  
  
real	0m1.220s  
user	0m1.216s  
sys	0m0.000s  
```  
  
But not with gcc-7.1 which is also noticeably faster all round:  
  
```  
jzm@jzm:~/boost/boost$ /usr/local/gcc/7.1.0/bin/g++ -I. -std=c++03 -O3 t.cpp && time ./a.out  
  
real	0m0.881s  
user	0m0.880s  
sys	0m0.000s  
jzm@jzm:~/boost/boost$ /usr/local/gcc/7.1.0/bin/g++ -I. -std=c++11 -O3 t.cpp && time ./a.out  
  
real	0m0.899s  
user	0m0.888s  
sys	0m0.000s  
```  
  
Going back to gcc-5.4.... if I preprocess the source in C++03 mode, then compile in C++11, I see no slowdown.... but, if I configure Boost with all the same macros set in both C++03 and 11 modes (so the Boost code is the same in both cases), then we're back to the slowdown again!!  
  
Possibly more to follow...

---

## Comment 3

> Username: kimwalisch  
> Created at: 2017-12-31 17:46:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354616370  

Yes, I also remember that using GCC 7.2.0 there was only a minor slowdown using ```-std=c++11``` in my program. As I mentioned in my first post I don't think this is purely a GCC <= 7.0 compiler performance bug because I was also able to reproduce the issue using a recent version of Clang (on both macOS an Ubuntu x64).

---

## Comment 4

> Username: jzmaddock  
> Created at: 2017-12-31 18:06:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354617237  

Update: found it - when BOOST_CONSTEXPR is defined to constexpr then it's 20-30% slower than when it's defined to nothing.  In other words simply annotating the functions with constexpr seems to be enough to produce the slowdown... need to investigate some more.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2017-12-31 19:39:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354620630  

Narrowed down some more:  
  
cpp_int.hpp:499 has:  
  
```  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR cpp_int_base(limb_type i)BOOST_NOEXCEPT  
      : m_wrapper(i), m_limbs(1), m_sign(false) {}  
```  
  
Which is called by the default constructor, which in turn is called from inside operator* which default constructs a temporary for the result.  As soon as I remove the constexpr from that function, the time difference disappears.  The same is true if I selectively remove constexpr from any of the parent class' default constructors (cpp_int_base, cpp_int or number).  
  
This is looking like a gcc bug (I'm unable to reproduce with current clang or msvc), but I'm going to ask on the mailing list about this just in case anyone else has any bright ideas.

---

## Comment 6

> Username: kimwalisch  
> Created at: 2017-12-31 21:11:53 UTC  
> Updated at: 2017-12-31 21:19:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354623990  

> This is looking like a gcc bug (I'm unable to reproduce with current clang or msvc), but I'm going to ask on the mailing list about this just in case anyone else has any bright ideas.  
  
I have re-run the test using Clang on Ubuntu 16.04 (x64) with boost 1.66.0:  
  
```bash  
$ clang++ --version  
clang version 3.8.0-2ubuntu4 (tags/RELEASE_380/final)  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
  
$ clang++ -I boost_1_66_0 -O2 test.cpp -o test  
$ clang++ -I boost_1_66_0 -O2 -std=c++11 test.cpp -o test_c++11  
  
$ time ./test  
Result: 12003693138042476126038783  
  
real	0m1.070s  
user	0m1.068s  
sys	0m0.000s  
  
$ time ./test_c++11   
Result: 12003693138042476126038783  
  
real	0m1.260s  
user	0m1.256s  
sys	0m0.000s  
```  
  
You have to be careful with recent compiler versions as e.g. GCC turns on ```-std=c++11``` by default since version 6. This may be why we can only reproduce the bug using GCC <= 5.x.  
  
And here are the Clang test results from my mac Book:  
  
```bash  
$ clang++ --version  
Apple LLVM version 9.0.0 (clang-900.0.39.2)  
Target: x86_64-apple-darwin17.3.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
  
$ clang++ -I boost_1_66_0 -O2 test.cpp -o test  
$ clang++ -I boost_1_66_0 -O2 -std=c++11 test.cpp -o test_c++11  
  
$ time ./test  
Result: 12003693138042476126038783  
  
real	0m1.017s  
user	0m1.014s  
sys	0m0.002s  
  
time ./test_c++11  
Result: 12003693138042476126038783  
  
real	0m1.180s  
user	0m1.175s  
sys	0m0.003s  
```  
  
So I was able to reproduce the issue also using Clang. That's why I think this is not simply a GCC <= 5.x performance bug.

---

## Comment 7

> Username: NAThompson  
> Created at: 2018-01-01 03:04:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354634458  

I found that there was a sizeable speedup compiling using `--std=c++1z`:  
  
```  
 time ./main.x  
Result: 12003693138042476126038783  
./main.x  1.17s user 0.00s system 99% cpu 1.183 total  
```  
  
vs 1.24 s for `--std=c++11`. Anyone see the same thing?  
  
Apple LLVM version 9.0.0 (clang-900.0.39.2)

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-01-01 09:51:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354645387  

OK, here's a more complete survey:  
  
```  
GCC     STD                 TIME  
4.8.5     -std=c++03       0.97  
4.8.5     -std=c++11       1.23  
4.8.5     -std=c++1y       1.21  
5.3.0     -std=c++03       1.08  
5.3.0     -std=c++11       1.21  
5.3.0     -std=c++14       1.27  
5.3.0     -std=c++1z       1.26  
6.3.0     -std=c++03       0.98  
6.3.0     -std=c++11       1.32  
6.3.0     -std=c++14       1.34  
6.3.0     -std=c++1z       1.32  
7.1.0     -std=c++03       0.87  
7.1.0     -std=c++11       0.89      <<<<< Fixed here???  
7.1.0     -std=c++14       0.89  
7.1.0     -std=c++1z       0.90  
```  
  
And with clang:  
  
```  
3.8     -std=c++03      1.35  
3.8     -std=c++11      1.34    <<<< No difference!  
5.0     -std=c++03      0.92  
5.0     -std=c++11      0.92  
5.0     -std=c++14      0.92  
5.0     -std=c++1z      0.92  
```  
  
So whatever clang is doing it is at least consistent.

---

## Comment 9

> Username: d25fe0be  
> Created at: 2018-01-01 10:01:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354645739  

I don't have GCC 7.1, but at least GCC 7.2 (Homebrew) with boost 1.66 (Homebrew) still seems to suffer from this problem:  
  
```  
$ cat 1.cpp  
#include <iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
using namespace std;  
using namespace boost::multiprecision;  
  
int main()  
{  
    __int128_t i = 0;  
    __int128_t j = 0;  
  
    int256_t sum = 0;  
  
    for (; i < 1e9; i += 19, j += 13)  
    {  
        int256_t diff = j;  
        sum += i * diff;  
    }  
  
    std::cout << "Result: " << sum << std::endl;  
  
    return 0;  
}  
$ g++-7 ./1.cpp -I /usr/local/include/ -O2 -std=c++03 && time ./a.out  
Result: 12003693138042476126038783  
  
real	0m0.923s  
user	0m0.917s  
sys	0m0.003s  
$ g++-7 ./1.cpp -I /usr/local/include/ -O2 -std=c++11 && time ./a.out  
Result: 12003693138042476126038783  
  
real	0m1.516s  
user	0m1.487s  
sys	0m0.009s  
$  
```  
  
`-std=c++14`/`-std=c++17` does not help, either.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2018-01-01 10:30:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354646677  

I'll look at 7.2 shortly, in the mean time, here's the minimal change which reproduces the issue:  
  
```  
--- a/include/boost/multiprecision/cpp_int.hpp  
+++ b/include/boost/multiprecision/cpp_int.hpp  
@@ -432,7 +432,7 @@ private:  
       double_limb_type   m_double_first_limb;  
   
       BOOST_CONSTEXPR data_type() : m_first_limb(0) {}  
-      BOOST_CONSTEXPR data_type(limb_type i) : m_first_limb(i) {}  
+      data_type(limb_type i) : m_first_limb(i) {}  
       BOOST_CONSTEXPR data_type(double_limb_type i) : m_double_first_limb(i) {}  
 #if defined(BOOST_MP_USER_DEFINED_LITERALS)  
       template <limb_type...VALUES>  
```

---

## Comment 11

> Username: jzmaddock  
> Created at: 2018-01-01 12:52:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354651885  

That's not what I see with 7.2:  
  
```  
jzm@jzm:~/boost/boost$ /usr/local/gcc/7.2.0/bin/g++ -I. -O3 -std=c++03 t.cpp && time ./a.out  
  
real	0m0.873s  
user	0m0.864s  
sys	0m0.000s  
jzm@jzm:~/boost/boost$ /usr/local/gcc/7.2.0/bin/g++ -I. -O3 -std=c++11 t.cpp && time ./a.out  
  
real	0m0.872s  
user	0m0.864s  
sys	0m0.000s  
jzm@jzm:~/boost/boost$ /usr/local/gcc/7.2.0/bin/g++ -I. -O3 -std=c++17 t.cpp && time ./a.out  
  
real	0m0.870s  
user	0m0.860s  
sys	0m0.004s  
```  
  
Note that I'm using -O3 not -O2 though it seems not to make much difference here.

---

## Comment 12

> Username: kimwalisch  
> Created at: 2018-01-01 13:37:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354653794  

> That's not what I see with 7.2  
  
I have also tested using GCC 7.2, boost 1.66 and Ubuntu 17.10 x64 and for me there is still a slowdown using ```-std=c++11```:  
  
```bash  
$ g++ --version  
g++ (Ubuntu 7.2.0-8ubuntu3) 7.2.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
$ g++ -I boost_1_66_0 -O2 -std=c++03 test.cpp -o test  
$ g++ -I boost_1_66_0 -O2 -std=c++11 test.cpp -o test_c++11  
  
$ time ./test  
Result: 12003693138042476126038783  
  
real	0m0.686s  
user	0m0.684s  
sys	0m0.000s  
  
$ time ./test_c++11   
Result: 12003693138042476126038783  
  
real	0m0.884s  
user	0m0.884s  
sys	0m0.000s  
```  
  
But after I used the very latest boost multiprecision library from GitHub (develop branch) the issue seems fixed!  
  
```bash  
g++ -I github_multiprecision  -std=c++11 test.cpp -o test_c++11  
  
$ time ./test_c++11   
Result: 12003693138042476126038783  
  
real	0m0.705s  
user	0m0.704s  
sys	0m0.000s  
```  
  
So you seem to have fixed the issue at least using the latest GCC 7.2!

---

## Comment 13

> Username: jzmaddock  
> Created at: 2018-01-01 16:38:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354662633  

OK good: I think that's the named-return-value optimization kicking in - glad to see it did some good!  
  
I don't have a fix for earlier GCC versions though - other than breaking the interface by making default-construct non-constexpr :(

---

## Comment 14

> Username: d25fe0be  
> Created at: 2018-01-01 16:48:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354663151  

I dumped the assembly using `gcc -S` (GCC 7.2, boost 1.66) and used `vimdiff` to compare the two.   
  
It looks that that `constexpr` somehow affects codegen more than merely eliminating the constructor (if possible at all). Register allocation (a few), code arrangement (I saw a block of code moved around) are all affected.  
  
Might this be a bug in GCC's cost model or something?  
  
ps: Just in case you want to investigate the assembly as well, I would suggest remove `std::cout << ... << sum << std::endl;` before doing that, as this can reduce the output quite a bit.

---

## Comment 15

> Username: kimwalisch  
> Created at: 2018-01-01 17:44:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/32#issuecomment-354665777  

> So you seem to have fixed the issue at least using the latest GCC 7.2!  
  
I now also confirm that there is no slowdown using ```-std=c++11``` using Clang 4.0 on Ubuntu 17.10 x64 using the latest boost multiprecision library from GitHub (develop branch). So this performance bug has been fixed for both GCC and Clang 👍    
  
> I don't have a fix for earlier GCC versions though - other than breaking the interface by making default-construct non-constexpr :(  
  
I don't care about old GCC versions all that much...
