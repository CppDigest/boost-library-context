# #25 - Cosine rounding failure on macOS [Open]

> Username: zfergus  
> Created at: 2020-02-28 19:59:00 UTC  
> Updated at: 2024-12-05 23:58:50 UTC  
> Url: https://github.com/boostorg/interval/issues/25  

I am running into issues with the interval library when compiling with Clang. This is the simple failure case:  
```c++  
#include <iomanip>  
#include <iostream>  
  
#include <boost/numeric/interval.hpp>  
  
typedef boost::numeric::interval<  
    double,  
    boost::numeric::interval_lib::policies<  
        boost::numeric::interval_lib::save_state<  
            boost::numeric::interval_lib::rounded_transc_std<double> >,  
        boost::numeric::interval_lib::checking_base<double> > >  
    Interval;  
  
int main(int argc, char* argv[])  
{  
    Interval theta = Interval(0.79358805865013693);  
    Interval output = cos(theta);  
    std::cout << std::setprecision(std::numeric_limits<double>::digits10 + 1)  
              << "[" << output.lower() << ", " << output.upper() << "]"  
              << std::endl;  
    std::cout << "is empty: "  
              << (output.lower() > output.upper() ? "true" : "false")  
              << std::endl;  
}  
```  
The output when compiled with clang is:  
```  
[0.7012920012119436, 0.7012920012119435]  
is empty: true  
```  
You can see that the last digit was rounded improperly resulting in an empty interval. If I try the same case but use `rounded_transc_exact` for the rounding policy It works as expected:   
```  
[0.7012920012119437, 0.7012920012119437]  
is empty: false  
```  
  
My compiler info is   
```  
Apple clang version 11.0.0 (clang-1100.0.33.17)  
Target: x86_64-apple-darwin19.3.0  
Thread model: posix  
```   
and the example is compiled in debug mode. The examples works fine on GCC (specifically I tested on `gcc (Ubuntu 9.2.1-9ubuntu2) 9.2.1 20191008`)  
  
Is this an expected failure for Clang?

---

## Comment 1

> Username: zfergus  
> Created at: 2020-02-28 21:55:04 UTC  
> Url: https://github.com/boostorg/interval/issues/25#issuecomment-592746789  

Debugging some more, I wanted to verify it would work for GCC on my mac. Using `g++-9 (Homebrew GCC 9.2.0_3) 9.2.0`, I still get the same rounding failure:   
```  
[0.7012920012119436, 0.7012920012119435]  
is empty: true  
```  
Could this be a problem with the hardware implementation of the trignometric functions? Is it guaranteed that `FE_DOWNWARD` cos(theta) < `FE_UPWARD` cos(theta)?

---

## Comment 2

> Username: zfergus  
> Created at: 2020-02-28 22:14:09 UTC  
> Updated at: 2020-02-28 22:16:33 UTC  
> Url: https://github.com/boostorg/interval/issues/25#issuecomment-592753309  

At this point, I have run it on five different machines:  
1. macOS with Intel(R) Core(TM) i7-7567U CPU: **fail**  
2. Ubuntu 19.10 with AMD EPYC 7452 32-Core Processor: **pass**  
3. Ubuntu 18.04.3 LTS with Intel(R) Core(TM) i7-5930K CPU: **pass**  
4. CentOS Linux with Intel(R) Xeon(R) CPU E5-2680 v4: **pass**  
5. CentOS Linux with Intel(R) Xeon(R) CPU E5-2690 v2: **pass**  
  
All compiled with GCC (of different versions ranging from 7 to 9).

---

## Comment 3

> Username: jeking3  
> Created at: 2024-12-05 23:13:07 UTC  
> Url: https://github.com/boostorg/interval/issues/25#issuecomment-2521695151  

Does this have any relation to #13?

---

## Comment 4

> Username: zfergus  
> Created at: 2024-12-05 23:58:29 UTC  
> Updated at: 2024-12-05 23:58:50 UTC  
> Url: https://github.com/boostorg/interval/issues/25#issuecomment-2521754528  

I don't think so, #13 was a compilation error if you did not specify the right rounding policy. This error is related to how the trigonometric functions are rounded using hardware rounding. We published a research article benchmarking various C/C++ interval arithmetic libraries. In it we found that the hardware rounding of transcendental functions is broken on certain platforms. We concluded [filib](https://github.com/zfergus/filib) to be the overall fastest and reliable library. You can read the full paper [here](https://cims.nyu.edu/gcl/papers/2022-Intervals.pdf).
