# #172 karatsuba multiplication in cpp_int [Closed]

> Username: madhur4127  
> Created at: 2019-11-27 17:04:08 UTC  
> Updated at: 2020-01-03 05:15:41 UTC  
> Closed at: 2020-01-03 05:15:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172  

Until now, `cpp_int` uses O(n**2) grade school multiplication which is highly optimized for not so small numbers. Note that there are other multiplication algorithms like Toom-3, Toom-4, FFT but for simplicity, karatsuba is chosen.  
  
The karatsuba cutoff is set to 100 limbs which is experimentally determined, although the limit could be as low as 10 limbs (as per GMP) but to avoid deep recursion depth 100 limbs is chosen.  
  
There is ~10% reduction in runtime for numbers falling beyond the cutoff value.  This can be improved further I guess because [python's karatsuba](https://hg.python.org/cpython/file/b514339e41ef/Objects/longobject.c#l2694) is around three times faster. I have tinkered around and I suspect that the `cpp_int` backend is slow as the algorithmic steps used are somewhat similar to that of python's.  
  
Compiling & Testing:  
1. Manually tested using randomly generated numbers as high having up to a million digits.  
2. Unit test passed  
3. No warnings were seen when compiled using `-Wall` using clang++ (v6.0.0) and g++(v7.3.2).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-11-27 18:16:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-559199241  

Thanks for this... it's been on the TODO list for far too long... will look into this in more detail later.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2019-11-27 19:13:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-559217348  

Just did a pass with cppcheck; everything looks good. Also ran a large number of the unit tests with `-fsanitize=address -fsanitize=undefined` and couldn't find anything.  
  
As to the speed, there seems to be a large number of calls to `std::copy`; is there any mechanism to elide that? Also, could you write a quick google benchmark so that we can look at the asymptotic scaling with bits? I think that might be very valuable to demonstrating that the asymptotic complexity is as expected.

---

## Comment 3

> Username: madhur4127  
> Created_at: 2019-11-28 08:24:44 UTC  
> Updated_at: 2019-11-28 08:27:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-559390136  

@NAThompson I wrote the google benchmark and here's the result (numbers in "Benchmark" denotes the bits in the input):  
  
```  
Running ./bin  
Run on (8 X 2400.01 MHz CPU s)  
CPU Caches:  
L1 Data 32K (x4)  
L1 Instruction 32K (x4)  
L2 Unified 256K (x4)  
L3 Unified 8192K (x1)  
Load Average: 0.37, 0.19, 0.12  
***WARNING*** Library was built as DEBUG. Timings may be affected.  
  
 Benchmark                   Time             CPU   Iterations  
BM_karatsuba/20000    8629269 ns      8628624 ns           81  
BM_karatsuba/21000    9393039 ns      9392341 ns           75  
BM_karatsuba/22000   10220446 ns     10219718 ns           68  
BM_karatsuba/23000   11103859 ns     11103020 ns           63  
BM_karatsuba/24000   11896585 ns     11895648 ns           59  
BM_karatsuba/25000   12895772 ns     12894698 ns           54  
BM_karatsuba/26000   13826824 ns     13825749 ns           51  
BM_karatsuba/27000   14790528 ns     14789312 ns           47  
BM_karatsuba/28000   16069344 ns     16068211 ns           45  
BM_karatsuba/29000   16787426 ns     16786438 ns           42  
BM_karatsuba/30000   17649477 ns     17648065 ns           40  
BM_karatsuba_BigO        1.39 f(N)       1.39 f(N)  
BM_karatsuba_RMS            3 %             3 %  
```  
**RMS:** Root Mean Square error  
**f(n):** n ^ 1.585 (complexity of karatsuba)  
Code:  
```cpp  
#include<iostream>  
#include<benchmark/benchmark.h>  
#include<boost/multiprecision/cpp_int.hpp>  
#include<boost/random.hpp>  
#include<cmath>  
using namespace boost::multiprecision;  
using namespace boost::random;  
static void BM_karatsuba(benchmark::State& state) {  
	mt19937 mt;  
	int bits = state.range(0);  
	uniform_int_distribution<cpp_int> ui((cpp_int(1) << (bits-1)), cpp_int(1) << bits);  
	for (auto _ : state){  
		cpp_int x = ui(mt), y = ui(mt);  
		x*y;  
	}  
	state.SetComplexityN(state.range(0));  
}  
double time(uint64_t n){  
	return pow((double)n, 1.585);  
};	  
BENCHMARK(BM_karatsuba)->DenseRange(20000, 30000, 1000)->Complexity(time);  
BENCHMARK_MAIN();  
```  
  
I think the coefficient as per benchmarking is O(1.39 n^1.585) which agrees with the theoretical complexity.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2019-11-28 13:37:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-559497745  

Cool; I'll try to run it under `perf` to see what asm instructions are causing the slowdown once I'm back on my linux box. But it's looking like everything is coming together.

---

## Comment 5

> Username: madhur4127  
> Created_at: 2019-12-09 14:13:23 UTC  
> Updated_at: 2019-12-09 14:13:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-563256201  

Hi @NAThompson. I ran `gprof` inorder to find the _costly_ functions, here's what I found:  
  
[Link](https://paste.ubuntu.com/p/gN4Qc7MrG2/)  
  
I cannot understand why `boost::multiprecision::backends::divide_unsigned_helper` is taking 85% of the time.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2019-12-09 15:24:59 UTC  
> Updated_at: 2019-12-09 15:34:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-563290551  

Your benchmark seems to be primarily benchmarking the performance of the `uniform_int_distribution`. Here's some changes which fix that:  
  
```cpp  
#include <iostream>  
#include <benchmark/benchmark.h>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/random.hpp>  
#include <cmath>  
  
using namespace boost::multiprecision;  
using namespace boost::random;  
  
static void BM_karatsuba(benchmark::State& state)  
{  
    mt19937_64 mt;  
    int bits = state.range(0);  
    uniform_int_distribution<cpp_int> ui((cpp_int(1) << (bits-1)), cpp_int(1) << bits);  
    cpp_int x = ui(mt), y = ui(mt);  
    cpp_int z;  
    for (auto _ : state)  
    {  
        ++x;  
        ++y;  
	benchmark::DoNotOptimize(z = x*y);  
    }  
    state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK(BM_karatsuba)->DenseRange(20000, 30000, 1000)->Complexity([](uint64_t n)->double { return std::pow(n, 1.585);});  
BENCHMARK_MAIN();  
```  
  
Here's the `perf report`:  
  
![perf_eval_multiply](https://user-images.githubusercontent.com/5459618/70448492-70d5b580-1a6e-11ea-94de-0f0b92639acc.png)  
  
And the flamegraph:  
  
![flame](https://user-images.githubusercontent.com/5459618/70448738-ce6a0200-1a6e-11ea-8317-89ab9601529b.png)  
  
And now the performance (of the benchmark) is greatly increased:  
  
```bash  
-------------------------------------------------------------  
Benchmark                   Time             CPU   Iterations  
-------------------------------------------------------------  
BM_karatsuba/20000     128321 ns       128320 ns         5384  
BM_karatsuba/21000     143719 ns       143718 ns         4801  
BM_karatsuba/22000     153467 ns       153467 ns         5236  
BM_karatsuba/23000     161462 ns       161460 ns         4325  
BM_karatsuba/24000     177092 ns       177094 ns         3943  
BM_karatsuba/25000     203530 ns       203531 ns         3424  
BM_karatsuba/26000     164983 ns       164983 ns         4235  
BM_karatsuba/27000     177194 ns       177194 ns         3938  
BM_karatsuba/28000     189899 ns       189896 ns         3679  
BM_karatsuba/29000     202780 ns       202779 ns         3434  
BM_karatsuba/30000     215142 ns       215140 ns         3221  
BM_karatsuba_BigO        0.02 f(N)       0.02 f(N)   
BM_karatsuba_RMS           10 %            10 %      
```  
  
If I am not mistaken, your benchmark measures the performance of multiplying different sized numbers *which occupy a fixed amount of memory*. Is this the relevant consideration? I was somewhat surprised by this, thinking that you would change the number of bits in the type, not the number of set bits in the arguments.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2019-12-09 15:55:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-563303767  

Note: I also get a warning on `g++-7`:  
  
```  
In file included from bench.cpp:3:0:  
../../boost/multiprecision/cpp_int.hpp: In function ‘void BM_karatsuba(benchmark::State&)’:  
../../boost/multiprecision/cpp_int.hpp:271:81: warning: ‘<anonymous>.boost::multiprecision::backends::cpp_int_base<0, 4294967295, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<long long unsigned int>, false>::m_data.boost::multiprecision::backends::cpp_int_base<0, 4294967295, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<long long unsigned int>, false>::data_type::ld.boost::multiprecision::backends::cpp_int_base<0, 4294967295, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<long long unsigned int>, false>::limb_data::data’ may be used uninitialized in this function [-Wmaybe-uninitialized]  
    BOOST_MP_FORCEINLINE limb_pointer limbs() BOOST_NOEXCEPT { return m_internal ? m_data.la : m_data.ld.data; }  
                                                                      ~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 8

> Username: madhur4127  
> Created_at: 2019-12-10 07:02:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-563895378  

@NAThompson Thanks for all the help! Really appreciate it sir!  
  
> Your benchmark seems to be primarily benchmarking the performance of the `uniform_int_distribution`.  
  
Thank you! I didn't knew that it will cause so much difference in runtime.   
  
> If I am not mistaken, your benchmark measures the performance of multiplying different sized numbers which occupy a fixed amount of memory.   
  
If I understand you correctly, your concern is that `x` and `y` are of same size in terms of number of bits then the rationale is:  It really doesn't matter as per the algorithm because I am halving based on the maximum bits of the two numbers so this determines the number of splits to perform. Therefore fixing the number of bits then the worst case can be achieved when the sizes are same otherwise the splits will make the multiplication end quickly.  
  
> Note: I also get a warning on `g++-7`:  
  
`-Wall` [should have triggered](https://gcc.gnu.org/onlinedocs/gcc/Warning-Options.html) this warning but I am unable to reproduce it on my machine. Here's `g++-7 -v`:  
```  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/7/lto-wrapper  
OFFLOAD_TARGET_NAMES=nvptx-none  
OFFLOAD_TARGET_DEFAULT=1  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 7.4.0-1ubuntu1~18.04.1' --with-bugurl=file:///usr/share/doc/gcc-7/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++ --prefix=/usr --with-gcc-major-version-only --program-suffix=-7 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-libmpx --enable-plugin --enable-default-pie --with-system-zlib --with-target-system-zlib --enable-objc-gc=auto --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04.1)   
```  
  
> Here's the `perf report`:  
  
Based on my understanding it reports that 92% of time is spent in `eval_multiply` but where in `eval_multiply` is unclear to me. But it proves that the my profiling using `gprof` is a blunder. I am digging deeper into the asm instructions. This is my first experience in profiling C++ code, I have got a lot to learn here.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2019-12-10 08:55:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-563932215  

FYI there are some existing performance tests in Boost.Multiprecision, but they've fallen a bit into disuse... I'm trying to revamp them now.  
  
If I had to guess, I suspect too much copying going on, but that's just a guess.

---

## Comment 10

> Username: NAThompson  
> Created_at: 2019-12-10 13:34:01 UTC  
> Updated_at: 2019-12-10 13:38:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-564034002  

Here's the assembly profile spat out by `perf` at the hottest location:  
  
![perf_disassembly](https://user-images.githubusercontent.com/5459618/70533773-c28e4680-1b27-11ea-914c-f2673816905e.png)  
  
[Here's](https://www.felixcloutier.com/x86/mulx) an explanation of `mulx`; I don't have enough context with integer asm to say if there is a better instruction or not.

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2019-12-10 19:30:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-564207030  

I would look into the performance, whereby I would like to investigate:  
* What is the cutoff bit count from school mul to Karatsuba mul?  
* Is the algorithm strictly for limb counst having a powr of 2?  
* I will look directly at the assembly code without any profiler or other instruments.  
  
I have cloned the fork.  
Could the author please provide me with a simple working example and any expectation regarding hot-spot of the algorithm?  
  
Thanks and kind regards, Chris

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2019-12-10 22:15:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-564284264  

I mean, that I will try to look into this, starting at the points mentioned above...  
Regards, Chris

---

## Comment 13

> Username: madhur4127  
> Created_at: 2019-12-11 08:25:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-564432133  

@ckormanyos, [Here's the brief sketch of the algorithm (from GMP)](https://gmplib.org/manual/Karatsuba-Multiplication.html).   
  
I have tweaked the ordering of the steps by using intermediate results as soon as possible in order to reduce memory consumption.  
  
> What is the cutoff bit count from school mul to Karatsuba mul?  
  
It's 100 limbs and a limb is 64 bits. So cut-off is 6400 bits. Note that both the numbers must be greater then the cutoff.  
  
> Is the algorithm strictly for limb counst having a powr of 2?  
  
No, both the numbers can be arbitary sized.  
  
Simple working example:   
```cpp  
#include<iostream>  
#include <boost/multiprecision/cpp_int.hpp>  
  
int32_t main(void){  
	using boost::multiprecision::cpp_int;  
	cpp_int a = ((cpp_int)1<<99000), b=((cpp_int)1<<900000)-1;  
	std::cout<<a*b<<std::endl;  
		  
	return 0;  
}  
```  
`a` and `b` can also be randomly initialised as in the [above comments](https://github.com/boostorg/multiprecision/pull/172#issuecomment-563290551).  
  
Here's my suspicion of the hotspot area:  
I think copying should be much of a concern because the same amount of _copy_ is present in python's karatsuba. Moreover the algorithmic steps are almost similar except that python is using just `int64_t` array (limbs) unlike wrapper of `cpp_int`.   
From my experiments with `cpp_int` and `gmp_int`, I found that the same integer object takes more time in `cpp_int` as compared to `gmp_int`. That's why I mentioned that I suspect that the backend is slow.  
All of this is mere speculation, I am looking for something concrete via the assembly code produced.

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2019-12-11 21:46:55 UTC  
> Updated_at: 2019-12-11 22:13:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-564747763  

I made a benchmark that cleanly isolates the near-pure multiplication time. It was set up for 100,000 mul operations. Here is the timing I got.  
This benchmark shows some promise for Karatsuba, but we need lots of work on it.  
My next step will be detailed analysis and code optimization investigations.  
Benchmark code can be found here:  
https://github.com/boostorg/multiprecision/blob/develop/example/cpp_int_mul_timing.cpp  
  
The benchmark does:  
* Implement a skinny pcg32 random generator to produce big random ints.  
* Initialize two std::vectors a[] and b[] with 100,000 big random ints.  
* multiply a[i]*= b[i] in a tight loop with timing via `std::chrono`  
  
```  
  // Time Boost.Multiprecision 10^5 mul schoolbook.  
  // (1024) total_time: 0.110293  
  // (2048) total_time: 0.390335  
  // (4096) total_time: 1.45973  
  // (8192) total_time: 5.42956  
  // (16384) total_time: 21.2418  
  // (32768) total_time: 83.5812  
  
  // Time Boost.Multiprecision 10^5 mul Karatsuba.  
  // (1024) total_time: 0.110478  
  // (2048) total_time: 0.39095  
  // (4096) total_time: 2.25638  
  // (8192) total_time: 6.79038  
  // (16384) total_time: 20.8101  
  // (32768) total_time: XXX memory (maybe stack overflow from recursion)  
  
  // Time ckormanyos proprietary 10^5 mul.  
  // (1024) total_time: 0.0458317  
  // (2048) total_time: 0.191055  
  // (4096) total_time: 0.716233  
  // (8192) total_time: 2.78224  
  // (16384) total_time: 10.9777  
  // (32768) total_time: 43.4864  
  
```

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2019-12-12 18:10:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565120730  

Just starting to look at this, there are some rather alarming looking warnings:  
  
```  
/usr/include/c++/9/bits/stl_algobase.h:386:23: warning: ‘void* __builtin_memcpy(  
void*, const void*, long unsigned int)’ forming offset [113, 408] is out of the   
bounds [0, 112] of object ‘a_l’ with type ‘boost::multiprecision::backends::cpp_int_backend<668, 668, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>’ [-Warray-bounds]  
  386 |      __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |      ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2152,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from performance_test_files/test33.cpp:8:  
../../../boost/multiprecision/cpp_int/multiply.hpp:148:79: note: ‘a_l’ declared here  
  148 |     cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h, a_l, b_h, b_l;  
      |                                                                               ^~~  
/usr/include/c++/9/bits/stl_algobase.h:386:23: warning: ‘void* __builtin_memcpy(void*, const void*, long unsigned int)’ forming offset [113, 408] is out of the bounds [0, 112] of object ‘b_l’ with type ‘boost::multiprecision::backends::cpp_int_backend<668, 668, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>’ [-Warray-bounds]  
  386 |      __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |      ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2152,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from performance_test_files/test33.cpp:8:  
../../../boost/multiprecision/cpp_int/multiply.hpp:148:89: note: ‘b_l’ declared here  
  148 |     cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h, a_l, b_h, b_l;  
      |                                                                                         ^~~  
/usr/include/c++/9/bits/stl_algobase.h:386:23: warning: ‘void* __builtin_memcpy(void*, const void*, long unsigned int)’ forming offset [113, 408] is out of the bounds [0, 112] of object ‘a_l’ with type ‘boost::multiprecision::backends::cpp_int_backend<668, 668, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>’ [-Warray-bounds]  
  386 |      __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |      ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2152,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from performance_test_files/test33.cpp:8:  
../../../boost/multiprecision/cpp_int/multiply.hpp:148:79: note: ‘a_l’ declared here  
  148 |     cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h, a_l, b_h, b_l;  
      |                                                                               ^~~  
/usr/include/c++/9/bits/stl_algobase.h:386:23: warning: ‘void* __builtin_memcpy(void*, const void*, long unsigned int)’ forming offset [113, 408] is out of the bounds [0, 112] of object ‘b_l’ with type ‘boost::multiprecision::backends::cpp_int_backend<668, 668, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>’ [-Warray-bounds]  
  386 |      __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |      ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2152,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from performance_test_files/test33.cpp:8:  
../../../boost/multiprecision/cpp_int/multiply.hpp:148:89: note: ‘b_l’ declared here  
  148 |     cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h, a_l, b_h, b_l;  
      |                                                                                         ^~~  
  
```  
  
and so on.  These look rather suspicious to me - maybe the cause of the crash you saw?

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2019-12-12 18:34:08 UTC  
> Updated_at: 2019-12-12 18:34:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565129397  

Actually, a more clear strategy seems to be forming up here. Basically, we seem to have a draft with a lot of promise, but maybe a few range problems, and also potentially open optimization issues.  
The traditional Boost satrategy comes to mind:  
* Get it working and correct.  
* Then optimize it.  
  
I think we need to look at the fundamental implementation of Karatsuba, its range checking, how memory is allocated in recursive calls and, etc.  
So I'm modifying my previous (primary) interest in optimization and will attempt to assist at first with getting ranges and memory straightened out.

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2019-12-12 18:50:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565135111  

Here's the performance results I'm seeing - looks promising so far:  
  
|Backend|128 Bits|256 Bits|512 Bits|1024 Bits|5120 Bits|10240 Bits|  
|-------|--------|--------|--------|---------|---------|----------|  
|cpp_int (existing code)|16.8653 (0.0362331s)|1.20857 (0.0496172s)|1.56989 (0.115773s)|1.80461 (0.379958s)|2.771 (8.67065s)|3.558 (33.4471s)|  
|cpp_int(fixed precision)|1 (0.00214838s)|1 (0.0410544s)|1.23286 (0.0909188s)|1.3664 (0.287694s)|   |   |  
|cpp_int(New code)|19.4126 (0.0176816s)|1.49443 (0.0246821s)|1.70751 (0.055705s)|1.87488 (0.18023s)|1.3 (4.13851s)|1.5 (14.185s)|  
|gmp_int|6.81998 (0.0146519s)|1.13428 (0.0465673s)|1 (0.0737462s)|1 (0.210548s)| 1 (3.12967s)|1 (9.40065s)|  
|tommath_int|25.4298 (0.0546329s)|1.8653 (0.0765788s)|1.66265 (0.122615s)|1.42111 (0.299212s)| 1.764 (5.52376s)|2.009 (18.8097s)|  
]

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2019-12-12 21:37:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565197892  

Cool. Could you please explain what you mean with "New code"? Is that the Karatsuba branch? What benchmark did you use?

---

## Comment 19

> Username: madhur4127  
> Created_at: 2019-12-13 04:25:55 UTC  
> Updated_at: 2019-12-13 05:54:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565295099  

How can I reproduce the above warnings?

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2019-12-13 06:04:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565312943  

I repeated some simplified tests and do see promising results. I do not yet see the desired performance. Unfortunately, I am becoming more certain that there are some out of bounds memory accesses, because I continue to see exceptions in the benchmarks I'm running.  
  
I have some old (1999) kara mult code. I can look into that, I remember allocating 4*N memory up front and sequentially using this linear chunk of memory (one-shot allocation) throughout the kara recursion.  
  
I will take a look and see if there are some rangfe issues in the new branch at handhere in 2019 for boost.

---

## Comment 21

> Username: madhur4127  
> Created_at: 2019-12-13 08:55:56 UTC  
> Updated_at: 2019-12-13 09:18:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565357461  

I think I have found the cause of the problem.  
  
**TL;DR**: [Copying the higher order bits (line 190)](https://github.com/boostorg/multiprecision/pull/172/files#diff-432a6e7a7526de762f2308c06749d4bf) which lies beyond the precision range causes runtime error.  
  
**Problem:**  
Consider the case of using fixed precision arithmetic. Suppose that the precision is `n` bits and the two numbers to be multiplied are also `n` bits wide, then the lower bound of the number of bits in the multiplication is `2*n-1`. But since the precision is `n` bits wide we cannot store the result completely.  
  
**Bug:**  
The bug is on [Line 190 of multiply.cpp](https://github.com/boostorg/multiprecision/pull/172/files#diff-432a6e7a7526de762f2308c06749d4bf). Here I try to copy bits which lie beyond `n` bits and which exceeds the precision in which the arithmetic is done thus there is a runtime error.  
  
**Possible Solution:**  
Prevent copying beyond the bits of precision.

---

## Comment 22

> Username: madhur4127  
> Created_at: 2019-12-13 09:26:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565367756  

@jzmaddock   
  
Assuming `cpp_int` (new code) is karatsuba's code then why is the execution time for `cpp_int` 's new code different from old code for bits <= 6400?   
  
Karatsuba cutoff is set to 100 limbs (6400bits) so there shouldn't be any difference between old and new code.

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2019-12-13 11:44:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565412363  

>Cool. Could you please explain what you mean with "New code"? Is that the Karatsuba branch? What benchmark did you use?  
  
@ckormanyos It's the version of libs/multiprecision/performance//performance_test in the "performance" branch (the develop version has got too old and broken to be much use out of the box).  And yes "new code" is with this PR merged into that branch.  
  
>How can I reproduce the above warnings?  
  
@madhur4127 cd into libs/multiprecision/performance and do a:  
  
../../../b2 toolset=gcc performance_test  
  
You may need to switch to, or merge the contents of the "performance" branch first, as develop has become outdated.  
  
>Assuming cpp_int (new code) is karatsuba's code then why is the execution time for cpp_int 's new code different from old code for bits <= 6400?  
  
I noticed that, but haven't investigated - since they test different code bases, they're from different builds and runs, which may be an issue.  But also you've changed a rather small compact function, into a much larger one - and often compact code, even when inefficient in the Big-O sense, will significantly outperform "smarter" code.  Remember that for small bit counts, this is really a very fast algorithm with tiny execution times, so small changes may have large and unexpected (not to mention annoying!) consequences.

---

## Comment 24

> Username: jzmaddock  
> Created_at: 2019-12-13 11:54:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565414968  

>I have some old (1999) kara mult code. I can look into that, I remember allocating 4*N memory up front and sequentially using this linear chunk of memory (one-shot allocation) throughout the kara recursion.  
  
Nod.  There are some rather obvious performance enhancements that could be made:  
  
1) The variables a_h, a_l, b_h, b_l could alias the existing limbs of the arguments rather than copy them.  
2) There could be one up-front memory allocation with all the temporaries aliasing it's memory.  
3) The left-shift and add at the end, could be fused into a single operation.  
  
(1) and (2) both require the same fix: allowing cpp_int_backend to alias external memory, I think that should be a fairly easy thing to enable, if you can give me some time I can take care of that.  
  
In the mean time, we need to see the correctness issues fixed, and probably a  dedicated test case for checking the algorithm (comparison to gmp results, as in test_cpp_int.cpp would be fine).  It might be helpful to macro-ize the cutoff limit for Karatsubu, so it can be lowered for testing too.

---

## Comment 25

> Username: madhur4127  
> Created_at: 2019-12-13 14:20:12 UTC  
> Updated_at: 2019-12-13 14:26:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565457592  

1. I have added the fix for runtime errors in fixed precision.   
2. macro-ized the cutoff limit for Karatsuba (line 73).  
  
I have stressed and found no issue with fixed precision arithmetic or arbitrary precision arithmetic. I think it should be now correct.  
  
> (1) and (2) both require the same fix: allowing cpp_int_backend to alias external memory  
  
I think it would be a premature optimization. I would like to do this but after we find the hotspot of the code. My concern is that the copying is only of order O(N logN) with a small constant factor. Moreover, the profiler also hints that copying is done only 10% of the time.  
  
My plan is to first make it as fast as python which copies in each recursive call and then other micro optimisations.  
  
> The left-shift and add at the end, could be fused into a single operation.  
  
Initially I did that but the resulting became ugly and there was almost no performance gain.  
  
> even when inefficient in the Big-O sense, will significantly outperform "smarter" code  
  
Exactly, this should must have happened. But if I am seeing this correctly, `cpp_int` (new code) is around twice faster in the table even for small bit counts which looked suspicious to me.

---

## Comment 26

> Username: madhur4127  
> Created_at: 2019-12-13 19:35:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565579229  

> ../../../b2 toolset=gcc performance_test  
  
Is the command correct or am I missing something?   
Running above command gives out this error even with clang instead of gcc:  
  
```  
clang-linux.link ../../../bin.v2/libs/multiprecision/performance/clang-linux-9.0.0/release/link-static/threading-multi/visibility-hidden/performance_test       
/usr/bin/ld: cannot find -lgmp                                                                                                                                  
/usr/bin/ld: cannot find -lmpfr                                                                                                                               clang-9: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
I am currently on `performance` branch and the tests in `libs/multiprecision/test` ran successfully. Moreover gmp is installed on my machine and `-lgmp` should have worked because [this example program works](https://gmplib.org/list-archives/gmp-discuss/2008-March/003085.html).  
```  
/usr/include/c++/9/bits/stl_algobase.h:386:23: warning: ‘void* __builtin_memcpy(  
void*, const void*, long unsigned int)’ forming offset [113, 408] is out of the   
bounds [0, 112] of object ‘a_l’ with type ‘boost::multiprecision::backends::cpp_int_backend<668, 668, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>’ [-Warray-bounds]  
  386 |      __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |      ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2152,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from performance_test_files/test33.cpp:8:  
../../../boost/multiprecision/cpp_int/multiply.hpp:148:79: note: ‘a_l’ declared here  
  148 |     cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h, a_l, b_h, b_l;  
      |                                                                               ^~~  
/usr/include/c++/9/bits/stl_algobase.h:386:23: warning: ‘void* __builtin_memcpy(void*, const void*, long unsigned int)’ forming offset [113, 408] is out of the bounds [0, 112] of object ‘b_l’ with type ‘boost::multiprecision::backends::cpp_int_backend<668, 668, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>’ [-Warray-bounds]  
  386 |      __builtin_memmove(__result, __first, sizeof(_Tp) * _Num);  
      |      ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
In file included from ../../../boost/multiprecision/cpp_int.hpp:2152,  
                 from ../../../boost/multiprecision/cpp_bin_float.hpp:9,  
                 from performance_test_files/test33.cpp:8:  
../../../boost/multiprecision/cpp_int/multiply.hpp:148:89: note: ‘b_l’ declared here  
  148 |     cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h, a_l, b_h, b_l;  
```  
  
Also these warnings gets doesn't show up when toolset is set to clang. Moreover I have no clue why offset 408 pops up in every warning (`forming offset [<...>, 408]`) over various precisions: 128, 512, 1024, 2048.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2019-12-13 20:58:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565606318  

>Running above command gives out this error even with clang instead of gcc:  
  
Strange.  But b2 should have spit out the command that failed, and that should provide a clue.

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2019-12-13 21:19:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565612409  

Thank you. After the pull of recent changes, the memory out of bounds issues are absent. Very nice. Awesome.  
  
This is an exciting time, because we have been interested in higher order multiplication algorithms in Boost.Multiprecision for quite some time. In fact, this algorithm, might also be appropriate for `cpp_bin_float`  
  
With the out-of-range errors now pleasantly gone, I can now provide more detailed timing reports. Using VC14.2 (2019) on x64. We see that Kara Mult is superior for hight digit counts. These trials use the unit kops/s so a high count means faster.  
  
```  
  // Boost.Multiprecision 1.71  
  // digits: 16384, kops_per_sec: 4.7  
  // digits: 32768, kops_per_sec: 1.2  
  // digits: 65536, kops_per_sec: 0.3  
  // digits: 131072, kops_per_sec: 0.075  
  // digits: 262144, kops_per_sec: 0.019  
  // digits: 524288, kops_per_sec: 0.0047  
  
  // Boost.Multiprecision + kara mult  
  // digits: 16384, kops_per_sec: 4.8  
  // digits: 32768, kops_per_sec: 1.6  
  // digits: 65536, kops_per_sec: 0.5  
  // digits: 131072, kops_per_sec: 0.15  
  // digits: 262144, kops_per_sec: 0.043  
  // digits: 524288, kops_per_sec: 0.011  
  
```

---

## Comment 29

> Username: ckormanyos  
> Created_at: 2019-12-13 21:22:56 UTC  
> Updated_at: 2019-12-13 21:23:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565613252  

I will try to look at some optimizations as well as numerical correctness soon.  
  
I'm not sure... but regarding, when Kara Mult kicks in, I thought limbs were unsigned integral values having 32 bits. This would mean kara mult kicks in at 3200 bits? Is this why it kicks in sooner than the 6000 or so?  
  
Regards, Chris

---

## Comment 30

> Username: ckormanyos  
> Created_at: 2019-12-13 21:30:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565615468  

In my post above, my timing report should use the word *bits* not digits

---

## Comment 31

> Username: madhur4127  
> Created_at: 2019-12-14 06:33:03 UTC  
> Updated_at: 2019-12-14 18:18:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565688969  

![Profiler](https://user-images.githubusercontent.com/29328609/70844464-639e3980-1e67-11ea-921a-f45dd98d9d40.png)  
  
I profiled the code (using ckormanyos's `example/cpp_int_mul_timing.cpp` with 202400 bits and 100 multiplication). Here's some findings which can reduce the execution time by almost half.  
  
In fixed precision arithmetic, every local variable in the stack calls memset, this accounts for 35% of the total time which is totally unnecessary as these will be initialised using std::copy anyway. Unfortunately, std::copy calls have been optimized and thus aren't visible but a crude guess would be atleast 20%.  
  
NOTE: memcpy which accounts for 15% of the time as shown in bottom left window is due to the initialisation of the numbers in `main` which is irrelevant to karatsuba.  
  
Thus this totals to at least 55% as per my guess.  
  
> (1) and (2) both require the same fix: allowing cpp_int_backend to alias external memory, I think that should be a fairly easy thing to enable, if you can give me some time I can take care of that.  
  
I hope, if this works then we could speed up the process substantially even faster than python!

---

## Comment 32

> Username: NAThompson  
> Created_at: 2019-12-14 13:26:46 UTC  
> Updated_at: 2019-12-14 14:13:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565717271  

@madhur4127: Could you repeat the analysis with [hotspot](https://github.com/KDAB/hotspot)? I'm not saying your benchmark is incorrect, but cachegrind has a lot of overhead not found in `perf`-based tools. In addition I didn't see much memcpy in the hot path of the asm using perf. (But that was a few iterations ago now.)

---

## Comment 33

> Username: madhur4127  
> Created_at: 2019-12-15 08:48:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565789464  

@NAThompson  
![image](https://user-images.githubusercontent.com/29328609/70859577-1a6be980-1f3c-11ea-9dfb-81d5b01a1149.png)  
  
Here's the comparison   
  
1. 200k bits:  
  
|Instruction| Hotspot (perf) | Kcachegrind (Valgrind) |  
|---| ---| --- |  
| brute force mul| ~40% | ~40%|  
|std::copy| NA | NA |   
| memset| 12.5 % | 35%|  
  
2. 800k bits  
  
Valgrind couldn't produce the output even after 15mins!  
  
|Instruction| Hotspot (perf) |  
|---| ---|   
| brute force mul| ~32.6% |  
|std::copy| NA |   
| memset| 19 % |

---

## Comment 34

> Username: ckormanyos  
> Created_at: 2019-12-15 08:51:43 UTC  
> Updated_at: 2019-12-15 08:58:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565789649  

Hi folks,  
  
I have numerous thoughts on this topic.  
  
@NAThompson: I think that is a good idea. But I suspect John has stated potential improvements above (as I also have these in mind). There are some really clear points.  
  
John writes:  
>   
    The variables a_h, a_l, b_h, b_l could alias the existing limbs of the arguments rather than copy them.  
    There could be one up-front memory allocation with all the temporaries aliasing it's memory.  
    The left-shift and add at the end, could be fused into a single operation.  
  
I wonder what the best way is to investigate this. Potentially *fast* code might implement separate low-level routines including schoolbook add, sub, mul, complement that operate on generic linear chunks of memory. In this design, we would start kara mult by allocating a large linear chunk of memory up front for results and perform add, sub, mul, complement with the basic low-level routines.  
  
The design present in the branch, however, uses existing high-level cpp_int objects and also allocates them and reallocates their memories for an intuitive and easy-to-read implementation.  
  
If we go the way of preparing low-level generic primitives for add, sub, mul, complement, then these could be used in other parts of multiprecision., But that would be a rather large design change. At that time, the question would be, ... Do we make these primitives base-2. Or do we make them generic base via stronger templating of not just the limb type and the double limb type, but the base of the limb as well?  
  
As it is written in the branch with the present intuitive style, I pick up a real and tangible speed up resulting from kara mult at 16384 bits. My historical recollection of improvement from kara mult (if done in a highly optimized fashion) is a lower bit count around 1000 to 4000. I'm not saying we could reach this. But that would be the naive target if we go for highly optimized code. And it is not immediately clear if the nice design of boost multiprecision should strive at all costs for high levels of optimization.  
  
Along the way, keep in mind that we can optimize allocate and reallocate, numbers of copies can be minimized. But the actual low-level operations of mul-shift-add-carry within the limbs themselves can not be optimized beyond what the compiler does without resorting to assembly code. And this is something which I would not necessarily recommend at all for boost at the moment.  
  
So to really move forward, I am wondering about how to ask two questions:  
1) Can the existing intuitive design be optimized while retaining its clarity with a few simple optimizations?  
2) Does any one want to look into a more significant redesign that provides generic add, sub, mul primitives that could be used generically throughout multiprecision?  
  
Kind regards, Chris

---

## Comment 35

> Username: ckormanyos  
> Created_at: 2019-12-15 14:14:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565813494  

> I will try to look at some optimizations as well as numerical correctness soon.  
  
I ran some independent home-grown DIY tests comparing results of the karatsuba branch with another non-boost, non-GMP big int lib, including specific tests for add, sub, mul, div at various bit counts, both smaller and above the kara mult cutoff.  
  
Results OK with no numerical errors and no other memory or range errors detected for 128, 512, 8192, 65536 bits.  
  
![test](https://user-images.githubusercontent.com/2404721/70863907-6a06e100-1f4d-11ea-85d5-89586e6ec503.png)

---

## Comment 36

> Username: jzmaddock  
> Created_at: 2019-12-15 16:42:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565825700  

>Can the existing intuitive design be optimized while retaining its clarity with a few simple optimizations?  
  
I'm hoping so.  
  
Now that I have our performance tests re-running again, I'm really rather heartened by some of the results - in particular the "cost" of wrapping a number up in class `number` and all that that means is virtually zero with modern compilers, for example for evaluating Bessel functions:  
  
<table class="table" summary="Bessel Functions (16 digit precision)">  
<colgroup>  
<col>  
<col>  
</colgroup>  
<thead><tr>  
<th>  
                <p>  
                  Type  
                </p>  
              </th>  
<th>  
                <p>  
                  Time  
                </p>  
              </th>  
</tr></thead>  
<tbody>  
<tr>  
<td>  
                <p>  
                  arithmetic_backend&lt;double&gt;  
                </p>  
              </td>  
<td>  
                <p>  
                  2.09301 (0.00133409s)  
                </p>  
              </td>  
</tr>  
<tr>  
<td>  
                <p>  
                  arithmetic_backend&lt;double&gt; - no expression templates  
                </p>  
              </td>  
<td>  
                <p>  
                  1 (0.000637403s)  
                </p>  
              </td>  
</tr>  
<tr>  
<td>  
                <p>  
                  double  
                </p>  
              </td>  
<td>  
                <p>  
                  1.07956 (0.000688113s)  
                </p>  
              </td>  
</tr>  
</tbody>  
</table>  
  
Which if taken literally, would mean that a double wrapped up in class `number` is actually faster than a naked `double` - it is of course just churn in the timing data.  There are similar results for integer tests as well.  
  
Of course the `cpp_int_backend` is rather more complex than just a wrapper around a native type, but still I'd like to see how far we can go with the current code base.

---

## Comment 37

> Username: jzmaddock  
> Created_at: 2019-12-15 20:17:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-565842876  

OK, I've pushed a branch "cpp_int_alias" which has one commit that implements aliasing: https://github.com/boostorg/multiprecision/commit/aa7d512558d7d4269af7b7a1ecf5a28fb62b82c2  
  
Usage is 2 fold, the constructor:  
  
```  
cpp_int_backend(limb_type* data, unsigned offset, unsigned len)  
```  
  
Is used for aliasing the limbs of another cpp_int (in the fixed precision case it just copies the data and there is no aliasing).  
  
There is also a nested type with constructor:  
  
```  
cpp_int_backend::scoped_shared_storage(cpp_int_backend& o, unsigned len)  
```  
  
which creates a shared pool of storage using the allocator of `o` and length `len`.  
  
temporaries which use the storage can then be constructed using:  
  
```  
cpp_int_backend(scoped_shared_storage& data, unsigned offset, unsigned len)  
```  
  
*do not* use the other aliasing constructor, as in the fixed precision case scoped_shared_storage is an empty do nothing object which always returns a NULL pointer.  
  
Which reminds me I must fix this up for C++03 (at least for now).

---

## Comment 38

> Username: jzmaddock  
> Created_at: 2019-12-16 19:40:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566211473  

OK, I made a right mess of the first cut at implementing aliasing, should now be fixed in the "cpp_int_alias" branch as before.

---

## Comment 39

> Username: madhur4127  
> Created_at: 2019-12-17 07:46:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566423858  

I added the code for karatsuba using alias in branch: `karatsuba_with_alias`.  
  
I have observed degradation in performance for fixed precision arithmetic but for arbitrary precision arithmetic, there is a ~10% improvement.  
  
I am looking over tricks to improve performance, if I am unable to do so then I will look for even faster algorithms like FFT/[DKSS](https://arxiv.org/pdf/1503.04955.pdf) which should work with fixed memory and no recursions.

---

## Comment 40

> Username: ckormanyos  
> Created_at: 2019-12-17 19:40:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566717503  

> I added the code for karatsuba using alias in branch: `karatsuba_with_alias`.  
  
> I have observed degradation in performance for fixed precision arithmetic but for arbitrary precision arithmetic, there is a ~10% improvement.  
  
I found no performance degradation for fixed in the `karatsuba_with_alias` branch on 2019.12.17.  
  
Kind regards, Chris  
  
```  
  // Boost.Multiprecision 1.72  
  // bits: 16384, kops_per_sec: 4.7  
  // bits: 32768, kops_per_sec: 1.2  
  // bits: 65536, kops_per_sec: 0.3  
  // bits: 131072, kops_per_sec: 0.075  
  // bits: 262144, kops_per_sec: 0.019  
  // bits: 524288, kops_per_sec: 0.0047  
  
  // Boost.Multiprecision + kara mult  
  // bits: 16384, kops_per_sec: 4.8 (4.8 karatsuba_with_alias branch 2019.12.17)  
  // bits: 32768, kops_per_sec: 1.6 (1.6 karatsuba_with_alias branch 2019.12.17)  
  // bits: 65536, kops_per_sec: 0.5 (0.5 karatsuba_with_alias branch 2019.12.17)  
  // bits: 131072, kops_per_sec: 0.15 (0.15 karatsuba_with_alias branch 2019.12.17)  
  // bits: 262144, kops_per_sec: 0.043 (0.043 karatsuba_with_alias branch 2019.12.17)  
  // bits: 524288, kops_per_sec: 0.011 (0.011 karatsuba_with_alias branch 2019.12.17)  
  
```

---

## Comment 41

> Username: ckormanyos  
> Created_at: 2019-12-17 19:50:25 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566721593  

> I am looking over tricks to improve performance, if I am unable to do so then I will look for even faster algorithms like FFT/DKSS which should work with fixed memory and no recursions.  
  
I can help with FFT_MUL. At the moment, I have fallen a bit out of contact with some of the new improvements in math. But does Boost have a fast real-to-half-complex FFT available?  
  
At the moment, kara mult can be very beneficial for bit counts exceeding 2^13, for both fixed and unbounded. This is potentially a good result that could be tested and does not seem to add much code.  
  
In general, the pursuit of high-level mul/div algorithms for Boost.Multiprecision seems like potentially a rich and well-applicable topic. Is there any interest in writing this up as a Boost GSoC project? After a long absence from the program due to personal matters, I am considering mentoring in 2020. But I wonder if anyone else would (also) like to mentor or generally go in in this direction?

---

## Comment 42

> Username: jzmaddock  
> Created_at: 2019-12-17 20:57:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566745715  

I've been experimenting with the performance of this code, here's what have on msvc x64.  
  
Here "this PR" is the code currently in this PR, merged to develop and the crossover set to 10 limbs - too low I know, but it's good to see where we are.  
  
"Aliased" is this PR with a few small modifications - 2 unneeded resizes were changed to asserts, and 4 temporaries were created from the same single allocation, plus a_l, b_l, a_h, b_h were all aliases (no allocations).  
  
|Type|bits| develop |this PR|Aliased|  
|-----|-----|--------|--------|--------------------------|  
|cpp_int(fixed) |128 |                    0.0170616| 0.0164909|0.0169627|  
|cpp_int(fixed) |256      |                                  0.031048|0.0296822|0.0317016|  
|cpp_int(fixed) |512       |                                  0.0843785|0.215765|0.227639|  
|cpp_int(fixed) |1024      |                                  0.299265|0.845088|0.782374|  
|cpp_int   |     128      |                                  0.0207817|0.0222652|0.0190269|  
|cpp_int     |   256       |                                  0.045632|0.0461168|0.041688|  
|cpp_int    |    512       |                                  0.144631|0.454839|0.27132|  
|cpp_int    |    1024      |                                  0.50678|1.55172|0.999653|  
|cpp_int   |     5120      |                                  11.4324|35.4739|19.6779|  
|cpp_int   |     10240     |                                  44.1753|109.084|58.9178|  
  
So.... up to precisions tested, the new code is slower, but we are getting closer to the crossover.  My gut says that the algorithm should ideally do better than this, I'll try and see what if anything we're missing another time.  
  
>I can help with FFT_MUL. At the moment, I have fallen a bit out of contact with some of the new improvements in math. But does Boost have a fast real-to-half-complex FFT available?  
  
No not at present.  Isn't the FFT even more heavyweight and needing super-high bit counts to make a difference?  
  
>In general, the pursuit of high-level mul/div algorithms for Boost.Multiprecision seems like potentially a rich and well-applicable topic. Is there any interest in writing this up as a Boost GSoC project? After a long absence from the program due to personal matters, I am considering mentoring in 2020. But I wonder if anyone else would (also) like to mentor or generally go in in this direction?  
  
Maybe.  I'd like to see if there's any more we can shave off this though.  
  
For the record, here's the "aliased" code I'm using:  
  
```  
   if(as >= karatsuba_cutoff && bs >= karatsuba_cutoff)  
   {  
      typename cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>::scoped_shared_storage storage(result, 4 * result.size());  
      cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> t1(storage, 0, result.size()), t2(storage, result.size(), result.size()), t3(storage, 2 * result.size(), result.size()), t4(storage, 3 * result.size(), result.size());  
        
      unsigned n = (as > bs ? as : bs) / 2 + 1;  
	   // write a, b as a = a_h * 2^n + a_l, b = b_h * 2^n + b_l  
      unsigned sz = (std::min)(as, n);  
      const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_l((limb_type*)a.limbs(), 0, sz);  
  
	   //a_l.resize(sz, sz);  
	   //std::copy(a.limbs(), a.limbs() + sz, a_l.limbs());  
  
	   sz = (std::min)(bs, n);  
      const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> b_l((limb_type*)b.limbs(), 0, sz);  
	   //b_l.resize(sz, sz);  
	   //std::copy(b.limbs(), b.limbs() + sz, b_l.limbs());  
  
      limb_type zero = 0;  
      sz = as > n ? as - n : 1;  
      limb_type* pl = as > n ? (limb_type*)a.limbs() : &zero;  
      const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> a_h(pl, n, sz);  
      /*  
	   if(as > n){  
		   a_h.resize(as - n, as - n);  
		   std::copy(a.limbs() + n, a.limbs() + as, a_h.limbs());  
	   }  
	   else  
	   {  
		   a_h.resize(1,1);  
		   a_h.limbs()[0] = 0;  
	   }  
      */  
      sz = bs > n ? bs - n : 1;  
      pl = bs > n ? (limb_type*)b.limbs() : &zero;  
      const cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> b_h(pl, n, sz);  
      /*  
	   if(bs > n)  
	   {  
		   b_h.resize(bs - n, bs - n);  
		   std::copy(b.limbs() + n, b.limbs() + bs, b_h.limbs());  
	   }  
	   else  
	   {  
		   b_h.resize(1,1);  
		   b_h.limbs()[0] = 0;  
	   }  
      */  
  
	   // x = a_h * b_ h  
	   // y = a_l * b_l  
	   // z = (a_h + a_l)*(b_h + b_l) - x - y  
	   // a * b = x * (2 ^ (2 * n))+ z * (2 ^ n) + y  
	   // result = | a_h*b_h  | a_l*b_l |  
	   // (bits)              <-- 2*n -->  
	   eval_multiply(t1, a_l, b_l);  
	   eval_multiply(t2, a_h, b_h);  
      BOOST_ASSERT(result.size() >= t1.size());  
	   //resize_for_carry(result, t1.size());  
	   std::copy(t1.limbs(), t1.limbs() + t1.size(), result.limbs());  
      BOOST_ASSERT(result.size() >= t1.size());  
      //resize_for_carry(result, 2 * n + t2.size());  
	   sz = (std::max<long long int>)(0, static_cast<long long int>((std::min)(result.size(), 2 * n + t2.size())) - static_cast<long long int>(2 * n));  
	   // for fixed precision arithmetic a_h and b_h cannot be non-zero  
	   // otherwise a_h * b_h cannot be placed in result.size() bits  
	   std::copy(t2.limbs(), t2.limbs() + sz, result.limbs() + 2 * n);  
  
	   eval_add(t1, t2);  // t1 = a_l*b_l + a_h*b_h  
	   eval_add(t3, a_l, a_h);  
	   eval_add(t4, b_l, b_h);  
	   eval_multiply(t2, t3, t4); // t2 = (a_h+a_l)*(b_h+b_l)  
	   eval_subtract(t2, t1);  
	   eval_left_shift(t2, n * limb_bits); // t2 = 2^n * ( (a_h+a_l)*(b_h*b_l) - a_h*b_h - a_l*b_l)  
	   eval_add(result, t2);  
  
	   result.normalize();  
	   result.sign(a.sign() != b.sign());	  
	   return ;  
	}  
  
```

---

## Comment 43

> Username: ckormanyos  
> Created_at: 2019-12-17 22:32:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566779198  

> So.... up to precisions tested, the new code is slower, but we are getting closer to the crossover. My gut says that the algorithm should ideally do better than this, I'll try and see what if anything we're missing another time.  
  
Yes. It is, even at this point, a fine improvement above 2^12 to 2^13 bits or so.  
  
What do you think? If kara mul gets reliable enough for `cpp_int`, could it also be applied to `cpp_bin_float` ?  
  
> No not at present. Isn't the FFT even more heavyweight and needing super-high bit counts to make a difference?  
  
Indeed. That is the realm of 10^5 bits and higher.  
  
> Maybe. I'd like to see if there's any more we can shave off this though.  
  
Cool. I think I might take a look in another implementation with raw memory to see if I can find out any additional information. I will also look deeply into the code you posted. Thank you.  
  
Kind regards, Chris

---

## Comment 44

> Username: NAThompson  
> Created_at: 2019-12-17 22:39:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566781386  

>  But does Boost have a fast real-to-half-complex FFT available?  
  
I've been wanting to deploy a DFT into Boost.Math for some time now; see how many times we `#include <fftw3.h>`; it's clearly useful. But *terrifyingly* difficult. Powers of two are fine; but check out the heroics FFTW went through to make prime length sequences transform in-plane in N log(N) time.  
  
My conclusion has been that FFTW has done this job right, and that deploying an FFT into Boost.Math would take a ton of time that could be more profitably applied elsewhere. But this is probably small consolation since you guys need a bitwise DFT for multiplication?

---

## Comment 45

> Username: madhur4127  
> Created_at: 2019-12-18 05:07:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-566869638  

```c++  
typename cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1>::scoped_shared_storage storage(result, 4 * result.size());  
cpp_int_backend<MinBits1, MaxBits1, SignType1, Checked1, Allocator1> t1(storage, 0, result.size()), t2(storage, result.size(), result.size()), t3(storage, 2 * result.size(), result.size()), t4(storage, 3 * result.size(), result.size());  
```  
t3, t4 could work with size of `n` limbs instead of `2 * n` (result.size()).  
  
> Is there any interest in writing this up as a Boost GSoC project? After a long absence from the program due to personal matters, I am considering mentoring in 2020. But I wonder if anyone else would (also) like to mentor or generally go in in this direction?  
  
I am currently an undergraduate student so I am eligible for participating in GSoC. Although I can also help in mentoring. Then we could also make some design changes to make the cpp_int objects lightweight enough to use for abstractions like in karatsuba.  
  
> My conclusion has been that FFTW has done this job right, and that deploying an FFT into Boost.Math would take a ton of time that could be more profitably applied elsewhere.  But this is probably small consolation since you guys need a bitwise DFT for multiplication?  
  
AFAICR, DFT then limbwise multiplication, then inverse DFT would be the steps. Since `fftw` claims itself to be the fastest among competing implementations it will require ton of time to get acceptable results.  
  
>  My gut says that the algorithm should ideally do better than this, I'll try and see what if anything we're missing another time.  
  
I am looking over to the text and other implementations to see if I can extract anything useful. Also I would like to make it _more_ cache friendly.

---

## Comment 46

> Username: jzmaddock  
> Created_at: 2019-12-19 13:02:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-567480459  

Update: tommath puts the Karatsuba cutoff at 80-limbs by default, which suggests we should be seeing a transition at around 5K bits - and indeed that's where tommath starts to outperform us.  I still have a gut feeling there's some operation(s) not really required in the this code, I just don't see it at present.  
  
>What do you think? If kara mul gets reliable enough for cpp_int, could it also be applied to cpp_bin_float ?  
  
cpp_bin_float uses cpp_int interally, so that "just happens" anyway.

---

## Comment 47

> Username: madhur4127  
> Created_at: 2019-12-20 08:08:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-567833302  

```c++  
x = a_h * b_ h  
y = a_l * b_l  
z = (a_h + a_l)*(b_h + b_l) - x - y  
a * b = x * (2 ^ (2 * n))+ z * (2 ^ n) + y  
  
result = | a_h*b_h  | a_l*b_l |  
(bits)              <-- 2*n -->  
```  
_Steps in karatsuba (for reference)_  
I have a few optimizations:  
1. t1 & t2 can be eliminated (optimized away) because we are copying them to the `result` anyway. Thus this saves half of the required memory.  
```c++  
eval_multiply(t1, a_l, b_l);  
eval_multiply(t2, a_h, b_h);  
// copy t1 & t2 to result.  
```  
  
2. `z` will not be calculated fully and its partial results will be straightaway added to the `result`. This idea is similar to loop fusion whereby the 3 parts of `z` : `x`, `y`, `(a_h+a_l)*(b_h+b_l)` instead of being calculated apriori are added to shifted `n*limb_bits` of result using a single loop.  
  
```c++  
eval_add(p, a_h*b_h,  a_l*b_l);  
eval_subtract((a_h+a_l)*(b_h+b_l), p); // will be eliminated  
z <<= n * limb_bits;  // will be eliminated  
eval_add(result, z);  
```  
  
I was against this code as it is eliminating all abstractions but this way it will elide the overhead of creating `cpp_int` objects in intermediate steps.

---

## Comment 48

> Username: madhur4127  
> Created_at: 2019-12-20 18:30:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568036871  

I made a bare-minimum implementation of the above idea on branch `karatsuba_with_alias` to test performance. It may not be accurate and may (or will) contain errors.  
  
Result are worse! It slowed down the code by ~15%.   
  
Valgrind's cachegrind claims that the new code has more cache hits making it more cache-friendly that I highly doubt.

---

## Comment 49

> Username: NAThompson  
> Created_at: 2019-12-20 18:35:29 UTC  
> Updated_at: 2019-12-20 18:36:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568038343  

> Valgrind's cachegrind claims that the new code has more cache hits making it more cache-friendly that I highly doubt.  
  
Try running your executable under `perf stat`, or  
  
```  
$ perf stat -e L1-dcache-load-misses ./bench.x  
```  
  
What does it tell you? (If you can't tell, I have zero confidence in cachegrind!)

---

## Comment 50

> Username: ckormanyos  
> Created_at: 2019-12-20 21:55:10 UTC  
> Updated_at: 2019-12-20 21:58:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568102037  

I have made the following observations.  
  
- Karatsuba multiplication seems like it potentially become significantly faster.  
- Best when using recursion based on limb counts having equal powers of 2.  
- Allocate memory up front, avoid costly shift and copy work.  
  
I created a first draft of a kara mul in another project and received these numbers.  
  
```  
  // Boost.Multiprecision + kara mult  
  // bits: 8192, kops_per_sec: 15 (15 karatsuba_with_alias branch 2019.12.17)  
  // bits: 16384, kops_per_sec: 4.8 (4.8 karatsuba_with_alias branch 2019.12.17)  
  // bits: 32768, kops_per_sec: 1.6 (1.6 karatsuba_with_alias branch 2019.12.17)  
  // bits: 65536, kops_per_sec: 0.5 (0.5 karatsuba_with_alias branch 2019.12.17)  
  // bits: 131072, kops_per_sec: 0.15 (0.15 karatsuba_with_alias branch 2019.12.17)  
  // bits: 262144, kops_per_sec: 0.043 (0.043 karatsuba_with_alias branch 2019.12.17)  
  // bits: 524288, kops_per_sec: 0.011 (0.011 karatsuba_with_alias branch 2019.12.17)  
  
  // wide_integer + ckormanyos draft of kara mult  
  // bits: 8192, kops_per_sec: 34  
  // bits: 16384, kops_per_sec: 11  
  // bits: 32768, kops_per_sec: 3.7  
  // bits: 65536, kops_per_sec: 1.2  
  // bits: 131072, kops_per_sec: 0.40  
  // bits: 262144, kops_per_sec: 0.13  
  // bits: 524288, kops_per_sec: 0.044  
  
```   
  
It should be possible to achieve something close to this in Boost.Multiprecision. But I would need to work a bit to identify the raw memory pointers. I would strongly recomment kara mult to be aligned on 2^n limb boundaries. The advantages get so overwhelming, that the extra play in limb count is less significant.  
  
You can also look very closely at my bare metal implementation draft to see a more clear algorithmc description with code that is easy-to-read.  
  
If you are interested in a second opinion on kara mul, please start with the subroutine `eval_multiply_kara` and the subroutines called by it here:  
https://github.com/ckormanyos/wide-integer/blob/master/wide_integer/generic_template_uintwide_t.h  
  
Kind regards, Chris

---

## Comment 51

> Username: ckormanyos  
> Created_at: 2019-12-20 22:15:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568107628  

Regarding the previous post...  
@madhur4127 I used the same limb-endianness in my draft. So basically, if there is a convenient way you can get close to that code, you should see a hopefully significant improvement.  
  
This code was quite difficult to write to be honest. The world-wide samples seem highly convoluded and descriptions often unclear. Hopefully, my example will provide improved clarity in this area.  
  
I can help get this into boost form if it seems like it's going in the right direction for us in boost. I hope I did not make any silly errors. But it seems to be looking promising.  
  
Kind regads, Chris

---

## Comment 52

> Username: ckormanyos  
> Created_at: 2019-12-21 13:19:01 UTC  
> Updated_at: 2019-12-21 13:19:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568180718  

> Update: tommath puts the Karatsuba cutoff at 80-limbs by default, which suggests we should be seeing a transition at around 5K bits - and indeed that's where tommath starts to outperform us. I still have a gut feeling there's some operation(s) not really required in the this code, I just don't see it at present.  
  
I just set my cutoff in my implementation to to 129 limbs, seeing improvement not quite at 4096 bits but strongly at 8192 bits.  
  
So if we can get something like that into boost improvement, that would be cool.  
  
Also... Earlier, I mentioned using buffer sizes of 2^n for kara mul. Ahigher granularity can be found by adding first harmonics at 3*2^n or higher harmonics at 5*2^n and 7*2^n. You just need to be sure to stop recursion at all the relevant base cases.  
  
> AFAICR, DFT then limbwise multiplication, then inverse DFT would be the steps. Since fftw claims itself to be the fastest among competing implementations it will require ton of time to get acceptable results.  
  
Karatsuba multiplication is the base case for higher order algorithms of the same kind, known as Toom Cook. Prior to FFT mul, Toom Cook is also something to look into.  
  
Kind regards, Chris

---

## Comment 53

> Username: madhur4127  
> Created_at: 2019-12-21 14:30:14 UTC  
> Updated_at: 2019-12-21 14:30:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568185064  

>https://github.com/ckormanyos/wide-integer/blob/master/wide_integer/generic_template_uintwide_t.h  
  
Differences:   
1. There is no allocation of memory on the stack so calls for memory allocation is avoided.  
2. Also one left shift is avoided.  
3. A subproblem: My formulation doesn't deal with the subproblem getting negative so overhead for calculating complement is not required.  
My code: `z = (a_h+a_l)*(b_h+b_l) - a_h*b_h - a_l*b_l`  
This code: `z= a_h*a_l + b_h*b_l - (a_h-a_l)*(b_h-b_l)`  
  
It is evident that the speedup increased from 2 to 4 (doubled) on increasing the bits by 64 times. Thus speedup instead of being a constant is growing with bits. This implies that the overhead is growing with the problem size which is a shock to me because some extra work is done on top of overhead to be done.  
  
Since John already added support for shared pool of storage, I am working on fix for 1 & 2.  
  
> $ perf stat -e L1-dcache-load-misses ./bench.x  
  
It says `<not-supported>` on all _hardware events_ like Icache load/miss, Dcache load/miss. I couldn't find [Coffee Lake](https://www.intel.in/content/www/in/en/architecture-and-technology/64-ia-32-architectures-software-developer-system-programming-manual-325384.html) in Intel's hardware manual to find the raw code for these events. I think because Intel dropped support from 8th gen.

---

## Comment 54

> Username: NAThompson  
> Created_at: 2019-12-21 15:29:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568189358  

> It says <not-supported> on all hardware events like Icache load/miss, Dcache load/miss. I couldn't find Coffee Lake in Intel's hardware manual to find the raw code for these events. I think because Intel dropped support from 8th gen.  
  
You can probably find something similar but still relevant with `perf list`. And what does `perf stat ./bench.x` say?

---

## Comment 55

> Username: ckormanyos  
> Created_at: 2019-12-21 17:25:01 UTC  
> Updated_at: 2019-12-21 18:01:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568197537  

> Since John already added support for shared pool of storage, I am working on fix for 1 & 2.  
  
Awesome! I'm excited to see what Boost can do. The raw memory pool seems available so should be similar speedup. Go for it. I will continue working on the case study as well.  
  
> A subproblem: My formulation doesn't deal with the subproblem getting negative...  
  
Cool. I will look into this.  
  
Kind regards, Chris

---

## Comment 56

> Username: ckormanyos  
> Created_at: 2019-12-21 18:05:24 UTC  
> Updated_at: 2019-12-21 18:27:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568200236  

> It is evident that the speedup increased from 2 to 4 (doubled) on increasing the bits by 64 times.  
  
You know, This whole kara mul thing is a pretty deep and rich topic. I still need to analyse the Boost branch code fully. I think I'm getting close to starting that.  
  
Another thing that surprises folks these days is that multiplication and shift are pretty much the same speed as copy and increment. Years ago, this was not the case in the old days when mul operations on a computer costed like 20 cycles. Today they cost one cycle, and that in the highly streamed pipeline. So pretty much any extra work being done in copy, shift or extra loop incrementation weighs much more heavily in terms of run-time penalty that some developers intutively expect.  
  
You need to make sure that your loops are tight, that when you can break from a loop, you recognize this and do break from it, etc.  
  
With time, we will get the performance in Boost that we crave...  
  
Keep going! Kind regards, Chris

---

## Comment 57

> Username: madhur4127  
> Created_at: 2019-12-23 12:44:59 UTC  
> Updated_at: 2019-12-23 12:46:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568465642  

It might be too soon but......  
  
I made a branch `new_implementation` with pre-allocation of shared pool for temporaries and complete aliasing.  
  
I was able to get around 40% more speedup as compared to this PR's karatsuba (as indicated by `time.cc` in branch `new_implementation`). Hopefully, If I make it correct then it should provide ~50% speedup as compared to brute force. There's a hope!  
  
Note: This implementation is not for fixed precision as the shared pool will be a do nothing object. This brings us to next question....  
  
> do not use the other aliasing constructor, as in the fixed precision case scoped_shared_storage is an empty do nothing object which always returns a NULL pointer. ([John's comment link](https://github.com/boostorg/multiprecision/pull/172#issuecomment-565842876))  
  
How to deal with fixed precision arithmetic?  
- Temporaries on stack  
- Brute force O(n^2)  
- Pre allocation and use std::copy

---

## Comment 58

> Username: jzmaddock  
> Created_at: 2019-12-23 12:59:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568468574  

> How to deal with fixed precision arithmetic?  
  
If you construct a fixed precision type from a scoped_shared_storage that's just fine - the constructor "does nothing" and the storage is internal.  What I was getting at, was don't call the scoped_shared_storage::limbs() member directly because in the wrong context you could get a NULL pointer.  I should realy make that member private and apply some friendship, but I haven't had time to figure that out yet.  
  
Also note that the sample code I posted above has a correctness bug somewhere, I'm trying to figure that out now.

---

## Comment 59

> Username: ckormanyos  
> Created_at: 2019-12-23 16:58:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568526108  

I actually would like to change a few of the architectural aspects of this development.  
Implementing Kara Mul could be quite simple and efficient. In the present direction being takin, I do not find it simple, relaiable and efficient. I would not want to build higher order Toom-Cook on top of this basis.  
  
Consider please another way of thinking. There is a point in eval_mul at which we know two limb pointers (pa, pb) and two requested precisions of the multiplication. Let's call the desired half precision n )the max of the two half precisions).  
  
It is at this point where we could implement a little bit of logic that decides which algorithm to use. We might need... fixed n*n->n, unbounded n*n->2n, scholl or kara.  
  
At this point there is no need for aliasing or any other sign changes or complicated operations that are confusing and slowing down this code. You can simply call the relevant primitives using raw limb pointers. I have shown this partly in my sample code. Copy and/or allocation can be done one time only, up front.  
  
When (or if) we move forward to Toom3,4, FFT we will want this kind of simple and robust architecture.  
  
Kind regards, Chris

---

## Comment 60

> Username: ckormanyos  
> Created_at: 2019-12-23 17:42:04 UTC  
> Updated_at: 2019-12-23 17:49:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568536925  

Another tip.  
  
It can be quite a simplification if you simply select a a judiciously defined variety of pre-sized buffers, let's say, _off the rack_ to place a,b,result,temp memories into.  
  
I usually suggest using buffer sizes that are 2^n or 3*2^n is size. If you use simple logic to identify the type of multiplication you need and copy your a and b into the pre-sized buffers (zero pad the unused limbs). Then I suspect you will find your Kara Mul to be much more simple to program and understand architecturally.  
  
You basically just need a handful of primitive functions such as add_n, sub_n, mul_n, etc. I also like to explicitly write separate functions that propagate the carry value or borrow bit, ensuring that these functions do actually break when carry or borrow are saturated and no longer needed down the entire memory line. But this is a design choice.  
  
Kind regards, Chris

---

## Comment 61

> Username: ckormanyos  
> Created_at: 2019-12-23 17:45:31 UTC  
> Updated_at: 2019-12-23 17:50:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568537788  

There may (not entirely sure, but maybe) also potentially some low-hanging fruit of optimization.  
  
I have found for the fixed (bounded) case, that our Boost `cpp_int` seems to be doing double the work that is actually required. I suspect that when we do `n*n->n` multiplication, that we are performing `n^2` operations. But in this reduced case, we need only to perform `1/2*n^2` of the full multiplication operations.  
  
My benchmarks show for the bounded case that my other project is exactly twice as fast as boost for low digit counts. If this proves to be the case (and I might be wrong), but if so, then a cool and easy optimization awaits for `cpp_int` and `cpp_bin_float`.  
  
I will double-check this.

---

## Comment 62

> Username: madhur4127  
> Created_at: 2019-12-23 18:20:55 UTC  
> Updated_at: 2019-12-23 18:30:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568545761  

Performance results from [maybe_karatsuba](https://github.com/madhur4127/multiprecision/tree/maybe_karatsuba) branch with [example/cpp_int_mul_timing.cc](https://github.com/madhur4127/multiprecision/blob/maybe_karatsuba/example/cpp_int_mul_timing.cpp) (modified):  
  
| bits * 2 | old kara | new kara |  
| -- | -- | --|  
| 16384 | 35.5039355039 | 36.9525369526 |  
| 32768 | 11.0728110728 | 17.5477175478 |  
| 65536 | 3.3692233693 | 7.4467674468 |  
| 131072 | 1.0228810229 | 3.1546331547 |  
| 262144 | 0.3011883013 | 1.1237311238 |  
| 524288 | 0.0775705776 | 0.3760373761|  
  
_Comparison (kops_per_sec): Higher the better_  
  
I could say that [this code](https://github.com/madhur4127/multiprecision/blob/54d4437264af3d9a42d44253cdac8e572c01e67f/include/boost/multiprecision/cpp_int/multiply.hpp#L74) is fast, modular (uses boost's primitive underneath).  
  
Note:   
1. I am doubtful with the case where `result.size()` isn't enough to store the multiplication's result, for now, I am **not** computing anything in this case (simple return).  
2. I stressed the code and there a bug somewhere occurs infrequently. So, this code is _almost_ correct but needing a minor bug fix.  
3. Old karatsuba cutoff was set to 100 limbs but now it is made to 80 limbs. BTW GMP suggests that it could be as low as 10 limbs.  
4. I think since the speedup is around 4 times which is also obtained by @ckormanyos using his earlier benchmark.  
  
**UPD**: In the benchmark using `b` bits, two cpp_int_backend objects are created with  `2b` bits wide. So the result is compluted fully and there is no premature return as of point (1).

---

## Comment 63

> Username: jzmaddock  
> Created_at: 2019-12-23 19:59:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568568175  

Looks good: just a word of caution that for non-fixed precision cpp_int's the line:  
  
```  
local::a[index] * local::b[index];  
```  
  
does nothing - the result is an expression template and no actual multipilcation is performed.

---

## Comment 64

> Username: jzmaddock  
> Created_at: 2019-12-23 20:01:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568568701  

>I have found for the fixed (bounded) case, that our Boost cpp_int seems to be doing double the work that is actually required. I suspect that when we do n*n->n multiplication, that we are performing n^2 operations. But in this reduced case, we need only to perform 1/2*n^2 of the full multiplication operations.  
  
Nope, it eludes unneeded multiplications.  
  
>My benchmarks show for the bounded case that my other project is exactly twice as fast as boost for low digit counts. If this proves to be the case (and I might be wrong), but if so, then a cool and easy optimization awaits for cpp_int and cpp_bin_float.  
  
It would be great if you can find a genuine difference somewhere.

---

## Comment 65

> Username: ckormanyos  
> Created_at: 2019-12-23 20:02:51 UTC  
> Updated_at: 2019-12-23 20:08:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568568926  

> I could say that this code is fast, modular (uses boost's primitive underneath).  
  
Very good. I think this is moving in the right direction.  
  
There still seems to be some confusion regarding how much memory is needed and how to deal with the fixed (bounded) case. This is actually quite confusing. So I will try to provide some clarity.  
  
- SIze a: precision of a, I think `as` in your code.  
- Size b: precision of b, I think `bs` in your code.  
- Total number limbs prior to multiplication (for both fixed and unbounded), I think `n` in your code  
- Temporary storage for result  
- Temporary storage for Karatsuba multiplication.  
  
A simple scheme that will work with no memory overrun at all is the following (we can optimize it later):  
  
0. Allocate for your inputs `1*n` for a and another `1*n` for b. Copy the inputs `pa` and `pb` into these and zero pad the unused limbs. Yes this is a redundant copy. We canoptimize it later.  
1. Allocate for your result storage `2*n` (surely safe since this is guaranteed to be larger than `2*max(as, bs))`. If you would like to avoid any memory problems whatsoever, then 2*n is always safe.  
2. Allocate `4*n` for your temporary storage for Karatsuba. You will use the lower `2*n` for the quadratic terms in your first two calls to Karatsuba. You need the separate upper `2*n` storage for your third and final call to Karatsuba mul.  
3. Then you enter your recursive Karatsuba.  
  
When you exit, you will have the entire result `2*n` in your allocated result buffer.  
  
In the fixed case, you copy up to `1*n` of this result into the result you need.  
In the unbounded case, you copy up to `2*n` of the result buffer into the result you need.  
  
When this works perfectly for bounded and unbounded, then you can optimize step 0, vie in-place use in Kara mul.  
  
You will note that your result is twice as large as it needs to be for the fixed case. This is fine for the first cut. Just disregard it. And get the overall scheme working.

---

## Comment 66

> Username: ckormanyos  
> Created_at: 2019-12-23 20:33:12 UTC  
> Updated_at: 2019-12-23 20:35:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568575046  

> It would be great if you can find a genuine difference somewhere.  
  
I hope I'm making a silly mistake such as internally using twice as many digits. But here are the results I just obtained. So if I set `number_of_digits` to 256, 512, 1024, etc. then there seems to be a performance differencs that is significant enough to notice.  
  
I'm probably overlooking something trivial. And that's ok if you explain it. But if this measurement is correct, then we might have a potential improvement.  
  
```  
// Other code (my sample code) using 1/2(n^2) school mul (see below)  
// bits: 256, kops_per_sec: 13600  
// bits: 512, kops_per_sec: 6160  
// bits: 1024, kops_per_sec: 2160  
  
// Boost 1.72 code with multiprecision type (see below)  
// bits: 256, kops_per_sec: 8760  
// bits: 512, kops_per_sec: 3010  
// bits: 1024, kops_per_sec: 910  
  
#if defined(BOOST_MULTIPRECISION_TEST_CPP_INT_USE_BOOST_CPP_INT)  
using big_uint_backend_type =  
  boost::multiprecision::cpp_int_backend<number_of_digits,  
                                         number_of_digits,  
                                         boost::multiprecision::unsigned_magnitude>;  
  
using big_uint_type = boost::multiprecision::number<big_uint_backend_type,  
                                                    boost::multiprecision::et_off>;  
#endif  
  
#if defined(BOOST_MULTIPRECISION_TEST_CPP_INT_USE_UINTWIDE_T)  
using big_uint_type = wide_integer::generic_template::uintwide_t<number_of_digits>;  
#endif  
  
```

---

## Comment 67

> Username: ckormanyos  
> Created_at: 2019-12-23 22:07:32 UTC  
> Updated_at: 2019-12-23 22:07:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568592744  

>> It would be great if you can find a genuine difference somewhere.  
  
> But if this measurement is correct, then we might have a potential improvement.  
  
As mentioned above, there might be an issue here. But it is a tough one to make all sides happy, whereby I literally mean both _sides_.  
  
In the fixed precision integral case, multiplication only needs to retain the lower half of the result.  
When, however, using `cpp_int` as the data member for `cpp_bin_float`, we need the upper half of the multiplication result. Each one of these two sides could theoretically be optimized, but we would need additional primitives for multiplication with retention of lower/upper half of result only.  
  
That's what I'm kind of seeing now.  
  
This is also very applicable to the main topic of this thread which is Karatsuba multiplication.  
We can sort out all of this stuff given a bit of time... At the moment nothing is broken.  
  
Kind regards, Chris

---

## Comment 68

> Username: jzmaddock  
> Created_at: 2019-12-24 09:45:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568709167  

I need to disappear from here for a few days (strangely!), but I just did a very quick performance comparison:  develop is obviously O(N^2) develop branch, JM is this version: https://github.com/boostorg/multiprecision/blob/cpp_int_alias_2/include/boost/multiprecision/cpp_int/multiply.hpp, and MC is the one that @madhur4127  linked to above: https://github.com/madhur4127/multiprecision/blob/maybe_karatsuba/include/boost/multiprecision/cpp_int/multiply.hpp  
  
Numbers are times taken for 0.5 million multiplies built using MSVC and default release options.  
  
| type| precision  |  develop  | JM  | MC |  
|-----|------------|-----------|-----|----|  
cpp_int(fixed) |128|  0.0205147 | 0.0206038 |0.0192592 |  
cpp_int(fixed) |256| |0.0404951 | 0.0411357 |0.0402448|  
cpp_int(fixed) |512 |0.123346 | 0.109974 |0.110488 |  
cpp_int(fixed) |1024 | 0.383677 | 0.379186 |0.449673 |  
cpp_int        |128  | 0.0236892| 0.0236506 |0.0242952 |  
cpp_int        |256  |0.0556233 | 0.0552206 |0.0633979 |  
cpp_int        |512  |0.173973 | 0.172107 |0.216305 |  
cpp_int        |1024 |0.627879 | 0.620223 |0.703012 |  
cpp_int        |1664 |1.61882 | 1.5482 | --- |  
cpp_int        |2048 |2.44533 | 2.2095 |2.35992 |  
cpp_int        |4096 |9.88414 | 7.11916 |7.64351 |  
cpp_int        |8192 |38.7116 | 22.3558 |24.3446 |  
cpp_int        |16384 |151.68 | 69.4554 |73.0438 |  
  
There are small differences which are mostly timing churn (I do see variation between runs), the 1664 bit run is to check the crossover point chosen for the JM code - 60 limbs, could possibly go slightly lower, but 50 was too low when I checked it.  MC has a slightly higher Karatsuba cutoff which may explain the slight timing differences, I haven't tried MC's code with other cutoffs.

---

## Comment 69

> Username: jzmaddock  
> Created_at: 2019-12-24 09:49:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568709814  

>In the fixed precision integral case, multiplication only needs to retain the lower half of the result.  
When, however, using cpp_int as the data member for cpp_bin_float, we need the upper half of the multiplication result. Each one of these two sides could theoretically be optimized, but we would need additional primitives for multiplication with retention of lower/upper half of result only.  
  
If it comes to it, it's relatively trivial to overload eval_multiply for fixed/variable precision and give each different code.  Note however that there are effectively 3 types here:  
  
* No allocator - always fixed.  
* Allocator, with upper limit on limb count: fixed precision with dynamic storage, only needed for fixed precision types with high bit counts.  
* Allocator, no upper limit.  True variable precision.  
  
BTW in the cpp_bin_float case, we don't just need the upper half - we need all of it in order to round correctly.

---

## Comment 70

> Username: jzmaddock  
> Created_at: 2019-12-24 09:50:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568710200  

Forgot to mention, MSVC => 32 bit limbs.  If I get a chance I'll repeat on Linux GCC-9 with 64 bit limbs.

---

## Comment 71

> Username: ckormanyos  
> Created_at: 2019-12-24 10:21:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568716581  

> If it comes to it, it's relatively trivial to overload eval_multiply for fixed/variable precision and give each different code.  
  
Nod. That's also what I was thinking. This would be something we could look into as an optional optimization if we decide to spend more time on the general topic of multiplication in Boost.Multiprecision (such as in a GSoC, if that comes to be).  
  
> BTW in the cpp_bin_float case, we don't just need the upper half - we need all of it in order to round correctly.  
  
Nod also. I realized this also a few hours after ruminating about it.  
  
Thanks and best regards, Chris

---

## Comment 72

> Username: ckormanyos  
> Created_at: 2019-12-24 10:27:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568717812  

> There are small differences which are mostly timing churn (I do see variation between runs), the 1664 bit run is to check the crossover point chosen for the JM code - 60 limbs, could possibly go slightly lower, but 50 was too low when I checked it.  
  
Yes. That is very nice. I will look more deeply into these two codes, the JM and MC versions. It seems like we are getting closer to the performance expected.  
  
I while ago I had written that I intuitively expect the Karatsuba cutoff to lie somewhere between 1,000 and 3,000 bits. So we are coming nicely into this sweet spot.  
  
I'll also be taking a day or two of less active time.  
  
Best regards, Chris.

---

## Comment 73

> Username: madhur4127  
> Created_at: 2019-12-24 10:38:36 UTC  
> Updated_at: 2019-12-24 10:49:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568720066  

I fixed the bug and optimized the memory storage in branch `maybe_karatsuba` that John linked in his comment.  
  
AFAICT for arbitrary precision, there are no issues, but for fixed precision we need to fix on something.  
  
@ckormanyos, I made the requirement to 5*result.size(). The reason for the extra `result.size()` from usual `4*result.size()` is that there can be overruns from this method of karatsuba implementation, your version made it very clear that size of each subproblem `u0-v0` cannot exceed `n` but `u0+v0` can exceed `n+1` in my formulation. I could use your approache but I found some tradeoffs:  
1. There are two comparisons (`compare_ranges`) that are avoided by my approach.  
2. Convenience to use boost's primitives.  
  
I don't want to compromise the speed in favor of (2) but there the memory overhead isn't much so I went ahead to use extra space to save comparisons (1).  
  
Kindly, let me know your thoughts!  
  
Meet you soon John, Chris, Nick. Happy Holidays!  
  
By the time you guys are back, I guess I will be finished tweaking the performance of `maybe_karatsuba`!  
  
**EDIT:**  
> Forgot to mention, MSVC => 32 bit limbs. If I get a chance I'll repeat on Linux GCC-9 with 64 bit limbs.  
  
I could do it on my machine and I also want to try out with/without compiler optimizations.

---

## Comment 74

> Username: ckormanyos  
> Created_at: 2019-12-24 11:26:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568729103  

> I don't want to compromise the speed in favor of (2) but there the memory overhead isn't much so I went ahead to use extra space to save comparisons (1).  
  
> Kindly, let me know your thoughts!  
  
The short answer is, if you're satisfied with the design and getting close to the performance and memory handling that you expect, then it's ok. 10, 20, 30 percent on performance or memory consumption up or down is not an issue. At that point, you stick to your design and fix all known issues and get it working.  
  
... And this will be a **very good result**!  
  
The longer answer is more philosophical in its nature. In Boost, we find sensible compromises on design. Would I have prefered a raw-memory oriented approach instead of the aliasing? Maybe, probably yes I would have intuitively done it differently, and also written and re-written a few primitives. THere are reasons (subjective ones also) for this also advantages, but disadvantages as well. But, as mwntioned, that can border on being subjective. In fact, I can live very happily with the current approach with aliasing and using existing primitives.  
  
Think of where we started... We were something like a factor of 3-4 off of the expected performance, and we were recursively allocating memory blocks, potentially causing a tough-to-debug situation. Now we are close to the expected performance with the clean memory allocation up front. At least I think this is where we are at. So this is good. And the design basis is solid.  
  
Keep going!  
  
Kind regards, Chris

---

## Comment 75

> Username: jzmaddock  
> Created_at: 2019-12-24 11:32:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568730144  

Here's the LInux results, with comparison to tommath and GMP, results marked (K) are where the first Karatsuba kicks in.  
  
|  bits | develop | JM | MC | GMP  | tommath |  
|-------|---------|----|----|------|---------|  
| 128   | 0.0136913| 0.0165019| 0.0165584| 0.00706893 | 0.0248222|  
|256  |0.0259292| 0.0242943| 0.0232063| 0.0166906 |0.0324572 |  
|512 |0.0677965|0.0543104|0.0545437| 0.0324923|0.0545702|  
|1024 |0.234184|0.172848|0.176557| 0.0959164 | 0.133638 |  
| 2048 |0.951793|0.78835|0.649706|0.30833 |0.426834|  
|4096 |3.7825|2.41435(K)|2.51504| 0.966158 |1.64487|  
| 8192 |14.6879|8.02402|8.06602(K)| 2.95522|5.5183|  
| 16384 |55.917| 24.4684|25.4746|8.58106|18.6911|  
  
The interesting one is tommath - like us it's all C (no assembler etc), it seems to be consistently slower for small bit counts - which doesn't surprise me as the routine is quite complex compared to us, but for bit counts above 1024 it begins to pull away.

---

## Comment 76

> Username: ckormanyos  
> Created_at: 2019-12-24 11:42:02 UTC  
> Updated_at: 2019-12-24 11:42:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568731606  

> The interesting one is tommath - like us it's all C (no assembler etc), it seems to be consistently slower for small bit counts - which doesn't surprise me as the routine is quite complex compared to us, but for bit counts above 1024 it begins to pull away.  
  
Which is good to know, and also a sensible verification of the proper direction of the JM and MC code designs. Boost and Tommath are a few tens of percent different. We are not talking about factors here. Indeed, a few tens of percent would not for me personally hardly be considered on the big-O scale. That GMP is a few factors ahead is inspirational to see how close we can get, but not surprising and actually also not a big deal on the big-O scale.  
  
If JM or MC code can hit such good performance, then what a boost for Boost!  
  
Kind regards, Chris.

---

## Comment 77

> Username: jzmaddock  
> Created_at: 2019-12-24 11:44:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568731968  

Just re-ran MC's code with the cutoff set to 60 limbs and it looks a bit better:  
  
|  bits | develop | JM | MC | GMP  | tommath |  
|-------|---------|----|----|------|---------|  
| 128   | 0.0136913| 0.0165019| 0.0165584| 0.00706893 | 0.0248222|  
|256  |0.0259292| 0.0242943| 0.0232063| 0.0166906 |0.0324572 |  
|512 |0.0677965|0.0543104|0.0545437| 0.0324923|0.0545702|  
|1024 |0.234184|0.172848|0.176557| 0.0959164 | 0.133638 |  
| 2048 |0.951793|0.78835|0.649706|0.30833 |0.426834|  
|4096 |3.7825|2.41435(K)|2.35613(K)| 0.966158 |1.64487|  
| 8192 |14.6879|8.02402|7.29971| 2.95522|5.5183|  
| 16384 |55.917| 24.4684|24.3436|8.58106|18.6911|  
  
The 8192 result looks like an outlier?  But you never know.

---

## Comment 78

> Username: ckormanyos  
> Created_at: 2019-12-24 13:12:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-568746067  

I just did a numerical verification of the JM code for fixed, unbounded, unsigned integer calculations.  
  
- Numerical results OK for add, sub, mul, div, sqrt at bits 128, 512, 8192, 12288, 65536.  
- First glance at memory handling OK.  
- Performance satisfactory (within almost a factor of 2 or so of ckormanyos bare metal). PM me if you'd like to delve deep into performance maybe in the future.  
  
I would not recommend squeezing the current design choice for highest performance. It is really shaping up, and I would be very happy with these potential improvements.  
  
It will be awesome with sauce to see if we can generate test cases (also for some high-precision math functions) when using something like the JM or MC code in combination with `cpp_bin_float`. I will try to begin to look into this after the holidays.  
  
Really nice progress on a tough topic folks!  
  
Kind regards, Chris

---

## Comment 79

> Username: madhur4127  
> Created_at: 2019-12-27 14:26:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569280483  

Sorry for the delay, I had to do some traveling.  
  
I am wondering which implementation should we stick to - JM / MC? So, that we can make it boost ready soon :)  
  
My 2 cents:  
I benchmarked and I think 60 is the higher cutoff for `maybe_karatsuba` it should be 50. Though the difference is not much till 2e5 bits. I tried to eliminate all copy so the algorithm works as if raw pointers are used. But still, the performance akin to raw pointers is not observed because a `cpp_int_backend` object is _heavy_ in terms of initialization.

---

## Comment 80

> Username: ckormanyos  
> Created_at: 2019-12-27 14:43:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569283379  

> Sorry for the delay, I had to do some traveling.  
  
Welcome back.  
  
> I am wondering which implementation should we stick to - JM / MC? So, that we can make it boost ready soon :)  
  
I think we need to decide together a few key issues. John and I have been working also in a coordinated way, but we have a few different points of focus. For instance, I have started to refactor cpp_int/multiply.hpp in some key areas in order to isolate the m-by-n multiplication interface in a more clear and easy to understand coding form (so I hope).  
  
We will eventually need to decide:  
  
- Raw pointers or higher-level primitives? Advantages/disadvantages, also constexpr implications.  
- Which kind of Karatsuba. At first even-even (ckormanyos) or jump right into even-odd (I believe this is what's been going on in this PR, but even-even might be a bit simpler).  
- With/without the need for aliasing the limb memory?  
- Refactor cpp_int/multiply.hpp or not? Before or after working draft?  
  
My personal preference would be to actually arrive at a medium-term strategy. Such as  
- Get a first prototype working...  
- First refactor cpp_int/multiply.hpp to get it ready to carry the weight of high-precision mul algos (not just Karatsuba but potentially even beyond).  
- Use raw pointers with an even-even Karatsuba multiplication.  
- Implement even-odd as an optimization  
  
P.S. It might take a bit longer to get this ready for Boost. It is thought that Boost.Multiprecision carries a rather significant load of world-wide code that uses it or relies on it in some way. We will be accordingly careful when modifying structures so central as the multiplication area.

---

## Comment 81

> Username: jzmaddock  
> Created_at: 2019-12-27 18:04:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569317877  

+1 to Chris's comments.  
  
> I am wondering which implementation should we stick to - JM / MC? So, that we can make it boost ready soon :)  
  
Probably some sort of hybrid - certainly I know there are a few features of your optimisations that I would pinch for mine if we went that route.  
  
At present though, I want to focus on correctness and make sure the foundations are solid before moving on or committing to any one piece of code.  We might also rewrite from scratch once we know the correct direction of travel.  Heaven only knows I've rewritten mine a few time already.  
  
Also.... and Chris may well hate this because it would substantially complexify the schoolbook algorithm, there is I suspect a way to speed it up substantially by partially unrolling the outer loop so that multiple (or at least 2 anyway) inner loops execute in parallel.  The key issue being that the algorithm is highly serial in nature, so the "obvious" implementation doesn't make good use of current hardware at all.  The idea would be similar to one proposed by Comba in Paul G. Comba, Exponentiation Cryptosystems on the IBM PC. IBM Systems Journal 29(4): 526-538 (1990) https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=2ahUKEwiJ8sD9s9bmAhVOQUEAHa-kD0AQFjAAegQIBBAC&url=https%3A%2F%2Fpdfs.semanticscholar.org%2F8b2b%2F98385baad2bdab2944f4e5f725ee237ca94f.pdf&usg=AOvVaw0Cji_qEnHgMPOJZulu7ryl

---

## Comment 82

> Username: ckormanyos  
> Created_at: 2019-12-27 18:50:47 UTC  
> Updated_at: 2019-12-27 18:51:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569325168  

>> I am wondering which implementation should we stick to - JM / MC? So, that we can make it boost ready soon :)  
  
> Probably some sort of hybrid - certainly I know there are a few features of your optimisations that I would pinch for mine if we went that route.  
  
Yes! That's the plan.  
  
> Also.... and Chris may well hate this because it would substantially complexify the schoolbook algorithm, there is I suspect a way to speed it up substantially by partially unrolling the outer loop so that multiple (or at least 2 anyway) inner loops execute in parallel.  
  
Oh quite to the contrary, John. I'm actually into it more than you might imagine. There is another idea that kind of comes from `cpp_dec_float`. The i-by-j loop in the binary integral back-end loops over two-dimensional rows and columns of limbs having 32 unsigned bits each, forcing us to multiply, add, carry, shift 32/64 bits in a tight O(N^2) loop. If we had just a few more bits, then we could completely sum up the columns of multiplication entirely before applying the carry to the next higher column. `cpp_dec_float` can do this within a few thousand i,j iterations because there are bits left over since the base is 10^8, which is a significant stride distance away from 32 bit binary. Now if we had a 96-bit unsigned integer synthesized in a fast way, then we could multiply, add and carry in the whole column before applying the carry to the next column, and I suspect parallelize them, assuming we stored a lot of columns in memory.  
  
Which means, I'm basically talking about using methods known from old-school assembly language programming. But made portable for C++. Would it be fast? Or simply clutter in the way? I do not know.  
  
I also have a parallel-for loop in my bag of tools. But you need to have an algorithm that can be parallelized for that.  
  
I will look more deeply into your link. Thank you.

---

## Comment 83

> Username: madhur4127  
> Created_at: 2019-12-28 05:19:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569386946  

> The key issue being that the algorithm is highly serial in nature, so the "obvious" implementation doesn't make good use of current hardware at all. The idea would be similar to one proposed by Comba in Paul G. Comba, Exponentiation Cryptosystems on the IBM PC. IBM Systems Journal 29(4): 526-538 (1990)  
  
>I also have a parallel-for loop in my bag of tools. But you need to have an algorithm that can be parallelized for that.  
  
Does this mean we're switching to parallel programming?  
Because if we could use concurrency then the karatsuba's subproblems can be evaluated in parallel and not requiring any change in _brute-force_ multiplication (serial) code.  
  
> The i-by-j loop in the binary integral back-end loops over two-dimensional rows and columns of limbs having 32 unsigned bits each, forcing us to multiply, add, carry, shift 32/64 bits in a tight O(N^2) loop.  
  
I might have reached 50 limb cutoff for `maybe_karatsuba`. This implies that all 50 limbs for both operands (64bit limb ~ 6.5kb) can fit into the cache and provide performance benefits much greater than making them parallel. However asm might help in speeding things up. Same goes for add/sub - break the problem until we make it to the cache.  
  
> Use raw pointers with an even-even Karatsuba multiplication.  
  
Karatsuba repeatedly calls add. So unless we make it pointer-based there would be no performance benefits.  
  
If we can agree in making pointer-based approach for multiplication in karatsuba, then using pointer-based approach throughout can increase the performance of other dependents. Ex - speeding up add/sub (using pointers) can increase performance of both mul & divide.   
  
The reason for uniformity is the abstraction which makes it convenient to use add/sub in mul/divide.  
  
So should I starting working on parallelizing Kara or making it pointer based? I would prefer the former.

---

## Comment 84

> Username: ckormanyos  
> Created_at: 2019-12-28 08:35:15 UTC  
> Updated_at: 2019-12-28 08:37:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569398488  

> Does this mean we're switching to parallel programming?  
  
Not yet. I think this can be considered as a (very interesting) side point perhaps for later. Sorry if we digressed.   
  
At the moment, we need to concentrate on the original focus of this thread/PR. In this thread, we should work toward at least one or two (single-threaded) working Karatsuba models that inprove efficiency in the Karatsuba range while simultaneously passing all existing Boost.Multiprecision and math tests.  
  
As mentioned by John (and I agree), the implementation might eventually turn into a hybrid using some boost primitives and perhaps breaking out at some abstracted software level to some low-level programming for the hardest-working inner loops.  
  
Assuming we get that, we could then talk about parallelization, and probably will.  
  
Best regards, Chris

---

## Comment 85

> Username: NAThompson  
> Created_at: 2019-12-28 14:29:11 UTC  
> Updated_at: 2019-12-28 14:30:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569421801  

> Does this mean we're switching to parallel programming?  
  
I understood John's comment as meaning writing code in such a way that AVX instructions are generated, rather than launching threads. I imagine launching threads in a multiply is the wrong parallelization granularity for almost all problems.

---

## Comment 86

> Username: jzmaddock  
> Created_at: 2019-12-28 15:12:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569424919  

>I understood John's comment as meaning writing code in such a way that AVX instructions are generated, rather than launching threads. I imagine launching threads in a multiply is the wrong parallelization granularity for almost all problems.  
  
That's what I was getting at yes (even good old fashioned x86 instructions would benefit from being scheduled against each other rather than each op being dependent on the previous one), I suspect launching threads inside `eval_multiply` is going to be way more expensive than the multiply itself.  It would be trivial to program with std::future and lambda's though should someone wish to experiment.

---

## Comment 87

> Username: ckormanyos  
> Created_at: 2019-12-28 15:44:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/172#issuecomment-569427054  

>>> Does this mean we're switching to parallel programming?  
  
>> I understood John's comment as meaning writing code in such a way that AVX instructions are generated, rather than launching threads. I imagine launching threads in a multiply is the wrong parallelization granularity for almost all problems.  
  
> That's what I was getting at yes (even good old fashioned x86 instructions would benefit from being scheduled against each other rather than each op being dependent on the previous one), I suspect launching threads inside `eval_multiply` is going to be way more expensive than the multiply itself.  
  
Cool thoughts.  
  
I have launched threads in an actual (yet unpublished) situation in a big number program. And this is, in fact a neat one to keep in mind. Let's say you want to go big. And you are rolling FFT multiplication with millions of bits. It then might make perfect sense to launch parallel threads for the FFTs in some way. There are several granularities possible. In FFT multiplication of `a*b->c`, we first perform forward transform of `a` and `b` independently. These entire forward transforms can be carried out in independent threads. It is also possible to break down individual large FFTs into portions that are fed into waiting threads.  
  
I would really enjoy getting into this stuff and more as we progress, in the future.  
  
At first for me, at the moment, getting kara mul and optimizing the important `n*n->n` mul loop would be seem like some good ways to start.  
  
Kind regards, Chris

---
