# #408 - Unit-test runs for 30 minutes when BOOST_TEST used in the loop [Open]

> Username: JBakamovic  
> Created at: 2024-01-15 17:23:05 UTC  
> Updated at: 2024-01-20 10:29:16 UTC  
> Url: https://github.com/boostorg/test/issues/408  

Hi, I ran into an interesting situation where I have a unit-test that takes forever to complete - 30 minutes - whereas the baseline for code-under-test is ~2 seconds, e.g. when running standalone without any test framework. Some overhead is of course expected, however, if I rewrite the unit-test in GoogleTest format, it also runs for ~2 seconds.  
  
I tested it against 1.81.0 and the most recent 1.84.0 which I compiled myself. The issue persists on both versions.  
  
This is the code that one shouldn't try to make sense of since it's carved out from the code I cannot show but it's sufficient to reproduce the issue, at least on my setup. What it basically does is that it invokes two different implementations of the same algorithm (foo vs bar) and validates that their output matches each other. Since the real-world implementation is very sensitive, unit-test must validate the output over the whole UINT32_MAX range.  
  
```  
#define BOOST_TEST_DYN_LINK  
#define BOOST_TEST_MAIN  
#define BOOST_TEST_MODULE quick_slow  
  
#include <boost/test/unit_test.hpp>  
#include <cstdint>  
#include <numeric>  
  
template <typename Integer>  
inline std::uint64_t foo(Integer value, uint8_t *& output) noexcept  
{  
    auto orig = output;  
    auto byte = static_cast<uint8_t>(value & 0xAB);  
    *output   = byte;  
    ++output;  
    return static_cast<std::uint64_t>(output - orig);  
}  
  
template <typename Integer>  
inline std::uint64_t bar(Integer value, uint8_t *& output) noexcept  
{  
    auto orig = output;  
    auto byte = static_cast<uint8_t>(value & 0xAB);  
    *output   = byte;  
    ++output;  
    return static_cast<std::uint64_t>(output - orig);  
}  
  
BOOST_AUTO_TEST_CASE(slow)  
{  
    for (uint32_t i = 0; i < std::numeric_limits<uint32_t>::max(); i++)  
    {  
        uint64_t foo_out = {0};  
        uint64_t bar_out = {0};  
  
        auto ptr1 = reinterpret_cast<uint8_t *>(&foo_out);  
        foo(i, ptr1);  
  
        auto ptr2 = reinterpret_cast<uint8_t *>(&bar_out);  
        bar(i, ptr2);  
  
        BOOST_TEST(foo_out == bar_out);  
    }  
}  
```  
  
On my machine, this code will run for ~30 minutes:  
  
```  
$ time ./bug -t "slow"  
Running 1 test case...  
  
*** No errors detected  
  
real 30m55.809s  
user 30m49.588s  
sys 0m0.092s  
```  
  
I built the code with  
  
> g++ -O3 -DNDEBUG -std=gnu++20 -o test test.cpp -lboost_unit_test_framework  
  
GCC version  
  
> g++ (GCC) 13.2.1 20231011 (Red Hat 13.2.1-4)  
> Copyright (C) 2023 Free Software Foundation, Inc.  
> This is free software; see the source for copying conditions.  There is NO  
> warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
Building and running it against Boost.Test 1.84.0 exhibits the same behavior.  
  
> g++ -O3 -DNDEBUG -std=gnu++20 -o test test.cpp -L/home/dev/Downloads/boost-1.84.0/stage/lib -l:libboost_unit_test_framework.so  
> LD_LIBRARY_PATH=/home/dev/Downloads/boost-1.84.0/stage/lib time ./bug -t "slow"  
> real 29m32.403s  
> user 29m28.831s  
> sys 0m0.076s  
  
I built the 1.84.0 Boost with  
  
> ./bootstrap.sh --with-python=python3  
> ./b2 stage -j64 threading=multi link=shared  
  
Since I believed that the issue might be with the BOOST_TEST repeatedly being invoked in a for-loop, I wrote another unit-test that moves the BOOST_TEST out of the for-loop and replaces it with the manual counter.  
  
```  
BOOST_AUTO_TEST_CASE(quick)  
{  
    uint64_t error_count = 0;  
    for (uint32_t i = 0; i < std::numeric_limits<uint32_t>::max(); i++)  
    {  
        uint64_t foo_out = {0};  
        uint64_t bar_out = {0};  
  
        auto ptr1 = reinterpret_cast<uint8_t *>(&foo_out);  
        foo(i, ptr1);  
  
        auto ptr2 = reinterpret_cast<uint8_t *>(&bar_out);  
        bar(i, ptr2);  
  
        if (foo_out != bar_out) error_count++;  
    }  
    BOOST_TEST(error_count == 0);  
}  
```  
  
That code runs in 2 seconds  
  
```  
$ time ./bug -t "quick"  
Running 1 test case...  
  
*** No errors detected  
  
real 0m1.906s  
user 0m1.899s  
sys 0m0.002s  
```  
  
And seems to suggest that the overhead of BOOST_TEST is very large. I also tried substituting it with the BOOST_CHECK and as well with some other macros such as BOOST_CHECK_EQUAL but the runtime performance did not change.
