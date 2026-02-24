# #1321 - Examples are all outdated/broken [Closed]

> Username: pps83  
> Created at: 2026-01-26 12:30:52 UTC  
> Updated at: 2026-01-27 18:10:18 UTC  
> Closed at: 2026-01-26 13:41:49 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321  

It seems that the code diverged so much that most of the code from examples https://develop.decimal.cpp.al/decimal/examples.html doesn't work.  
Starting from typedefs that do not exist (like `decimal32_t`), or ctors from strings that aren't there anymore

---

## Comment 1

> Username: pps83  
> Created at: 2026-01-26 12:54:14 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799469630  

I use just a few functions from intel's libbid, and tried to check string->decimal conversion.  
Sad that the normal ctor from string isn't there now, so I checked examples folder instead of the webpage and even basic example doesn't have that conversion from string anymore. With some digging, I figured it out:  
  
```  
    const char* c_string_value{"1"};  
    //const decimal32 from_c_string{c_string_value};  
  
    decimal64 return_value;  
    from_chars(c_string_value, c_string_value + std::strlen(c_string_value), return_value);  
```  
vs using intel's lib:  
```  
    unsigned int f;  
    auto x1 = __bid64_from_string((char*)c_string_value, 0, &f);  
    auto x2 = return_value.bits_;  
```  
at the end, the values do not match, while I expected them to be identical:  
  
> x1: 0x31c0000000000001  
> x2: 0x2238000000000001

---

## Comment 2

> Username: mborland  
> Created at: 2026-01-26 13:30:41 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799619295  

What version are you using? Develop, master, or v6 are what's represented in the docs. The examples aren't written into the documentation pages, they are directly inlined from cpp files in `examples/` folder which is run in its entirety every CI run.

---

## Comment 3

> Username: pps83  
> Created at: 2026-01-26 13:30:50 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799619947  

it appears that I need to do this conversion to match output of `__bid64_from_string`.  
```  
auto x2 = boost::decimal::to_dpd(return_value);  
```  
So, conversion to dpd matches output of `__bid64_from_string`, and to match output of `boost::decimal::from_chars` I need to convert value returned by `__bid64_from_string` using `__bid_to_dpd64`. This is kind of strange

---

## Comment 4

> Username: pps83  
> Created at: 2026-01-26 13:31:38 UTC  
> Updated at: 2026-01-26 13:32:40 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799623249  

> What version are you using? Develop, master, or v6 are what's represented in the docs. The examples aren't written into the documentation pages, they are directly inlined from cpp files in `examples/` folder which is run in its entirety every CI run.  
  
I use `develop` branch. Which one should I use?

---

## Comment 5

> Username: mborland  
> Created at: 2026-01-26 13:35:13 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799639568  

> > What version are you using? Develop, master, or v6 are what's represented in the docs. The examples aren't written into the documentation pages, they are directly inlined from cpp files in `examples/` folder which is run in its entirety every CI run.  
>   
> I use `develop` branch. Which one should I use?  
  
Any one of current develop, master, or v6 will run the examples you describe. I am on current develop and just ran `examples/promotion.cpp` just fine which contains the string constructors:  
  
```c++  
// First construct two values that we will perform arithmetic with  
const decimal32_t a {"5.2"};  
const decimal64_t b {"3.9"};  
```

---

## Comment 6

> Username: pps83  
> Created at: 2026-01-26 13:41:20 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799676772  

Looks like false alarm. I had it checked at my old fork, I reset it to current develop and it now matches `__bid64_from_string` now.

---

## Comment 7

> Username: pps83  
> Created at: 2026-01-26 13:41:49 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799679427  

issue was due to invalid/old fork that didn't have correct results

---

## Comment 8

> Username: mborland  
> Created at: 2026-01-26 13:42:58 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799685731  

Glad it was an easy fix

---

## Comment 9

> Username: pps83  
> Created at: 2026-01-26 13:49:10 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799715846  

> Glad it was an easy fix  
  
I had it checked out like a year ago, around the time when you created int128. So, looks like it was actually doing dpd instead of bid under the hood (I'm not familiar what's the difference between these things)

---

## Comment 10

> Username: mborland  
> Created at: 2026-01-26 13:54:44 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3799739081  

Yes, there was an encoding issue a while back that was resolved prior to second review

---

## Comment 11

> Username: pps83  
> Created at: 2026-01-27 08:07:58 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3803709943  

I wrote a basic test that crates random 10000 uints as strings, then parses them to decimal64_t using boost::decimal and intel libs. Results are identical, however, basic parsing somehow is over 2x faster with intel lib.  
  
<img width="609" height="190" alt="Image" src="https://github.com/user-attachments/assets/09c2cc30-ae7d-4353-8578-0b41a990d62e" />  
  
Did you use latest intel's lib or the one maintained by gcc? It looks like intel ppl are making changes/fixes [directly in gcc](https://github.com/gcc-mirror/gcc/commits/master/libgcc/config/libbid).

---

## Comment 12

> Username: pps83  
> Created at: 2026-01-27 08:12:50 UTC  
> Updated at: 2026-01-27 08:14:04 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3803735447  

Here's sample code I used for testing. `nanobench.h` is simply this file: https://github.com/martinus/nanobench/blob/master/src/include/nanobench.h  
it's not as precise, but good enough to see the results.  
  
test generates random uints as string and parses them to decimal, and then parses the same number with "0." prepended   
  
```c++  
#define ANKERL_NANOBENCH_IMPLEMENT  
#include "gtest/gtest.h"  
#include <nanobench.h>  
#include <boost/decimal.hpp>  
#include <random>  
#define NANOBENCH(...) ankerl::nanobench::Bench().run(#__VA_ARGS__, [&]() { __VA_ARGS__; })  
  
extern "C" unsigned long long __bid64_from_string(char*, unsigned int, unsigned int*);  
  
unsigned long long stringToDecimal(const char* str)  
{  
    unsigned int flags = 0;  
    return __bid64_from_string(const_cast<char*>(str), 0, &flags);  
}  
  
TEST(Decimal, FromString)  
{  
    std::random_device rd;  
    std::vector<std::string> nums;  
    const int N = 10000;  
    for (int i = 0; i < N; ++i)  
    {  
        char buf[32];  
        sprintf(buf, "0.%u", rd());  
        nums.emplace_back(buf);  
    }  
  
    std::vector<uint64_t> nums_intel(N), nums_boost(N);  
    for (int n = 0; n < 2; ++n)  
    {  
        auto convertIntelInt = [&nums_intel, &nums, N, n]() {  
            for (int i = 0; i < N; ++i)  
                nums_intel[i] = stringToDecimal(nums[i].c_str() + n * 2);  
        };  
        auto convertBoostInt = [&]() {  
            for (int i = 0; i < N; ++i)  
                nums_boost[i] = to_bits(boost::decimal::decimal64_t(nums[i].c_str() + n * 2));  
        };  
        NANOBENCH(convertIntelInt());  
        NANOBENCH(convertBoostInt());  
        ASSERT_EQ(nums_intel, nums_boost);  
    }  
}  
```

---

## Comment 13

> Username: mborland  
> Created at: 2026-01-27 13:34:58 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3805254571  

> Did you use latest intel's lib or the one maintained by gcc? It looks like intel ppl are making changes/fixes [directly in gcc](https://github.com/gcc-mirror/gcc/commits/master/libgcc/config/libbid).  
  
I have been using Intel's lib directly off their website so that I could test it with multiple compilers. GCC used to bundle the IBM library so that gave an additional data point.

---

## Comment 14

> Username: pps83  
> Created at: 2026-01-27 15:35:59 UTC  
> Updated at: 2026-01-27 15:36:23 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3805899489  

> I have been using Intel's lib directly off their website  
  
this one is bad imo :) the one from their site is outdated. They released updated version, but their own site still lists the old one.  
  
I took updated version and made some fixes (including buffer overrun fixes): https://github.com/pps83/IntelRDFPMathLib (it has proper visual studio project files if you use VS)

---

## Comment 15

> Username: mborland  
> Created at: 2026-01-27 15:48:30 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3805968074  

> > I have been using Intel's lib directly off their website  
>   
> this one is bad imo :) the one from their site is outdated. They released updated version, but their own site still lists the old one.  
>   
  
They do have a link on the site for this version which I think is the latest: https://www.netlib.org/misc/intel/. At least it works with the new clang based intel compiler, because I can't find the classic compiler anywhere anymore.  
  
> I took updated version and made some fixes (including buffer overrun fixes): https://github.com/pps83/IntelRDFPMathLib (it has proper visual studio project files if you use VS)  
  
I'll take a look. Thanks.

---

## Comment 16

> Username: pps83  
> Created at: 2026-01-27 18:10:18 UTC  
> Url: https://github.com/boostorg/decimal/issues/1321#issuecomment-3806720698  

Yes, netlib has u3 version with changes for icx (llvm based intel compiler)
