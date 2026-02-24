# #52 Added constexpr support to min/max functions of random number generators. Fixes #12251 [Closed]

> Username: rtimmons  
> Created at: 2019-03-22 19:19:06 UTC  
> Updated at: 2019-04-01 18:25:39 UTC  
> Closed at: 2019-04-01 18:20:47 UTC  
> Url: https://github.com/boostorg/random/pull/52  

This takes #24 as a base and adds some testing.  
  
I'm new to developing on boost so I hope this level of testing is sufficient.  
  
1. I ran the self-tests with `bjam --toolchain {clang,gcc}` and it showed all tests in the `test` dir as passing.   
2. I also ran with clang in C++17 mode (which has constexpr support) via `b2 --abbreviate-paths address-model=64 toolset=clang cxxflags="-std:c++17"`.  
3. ↑ This revealed that a few of the engines from pr #24 aren't compatible with `constexpr` because some of their underlying dependencies (e.g. `boost::multiprecision`) aren't compatible with `constexpr`. I left these files untouched, but I'd be happy to add comments indicating why they don't have `BOOST_CONSTEXPR` but the others do. Just let me know.  
4. Ideally we'd have `constexpr` everywhere within boost.random, but having it in as many places as possible seems better than having it nowhere.  
  
Thank you for your work in maintaining boost.random. I hope this is a useful addition - it will definitely help in my current project work.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2019-03-28 17:22:58 UTC  
> Url: https://github.com/boostorg/random/pull/52#issuecomment-477693039  

AMDG  
  
On 3/22/19 1:19 PM, Ryan Timmons wrote:  
> This takes #24 as a base and adds some testing.  
>   
> I'm new to developing on boost so I hope this level of testing is sufficient.  
>   
  
The right way is to integrate it into test_generator.ipp  
so that it is automatically applied to all engines.  I'll  
handle this shortly.  
  
> 1. I ran the self-tests with `bjam --toolchain {clang,gcc}` and it showed all tests in the `test` dir as passing.   
> 2. I also ran with clang in C++17 mode (which has constexpr support) via `b2 --abbreviate-paths address-model=64 toolset=clang cxxflags="-std:c++17"`.  
  
You can use cxxstd=17  
  
> 3. ↑ This revealed that a few of the engines from pr #24 aren't compatible with `constexpr` because some of their underlying dependencies (e.g. `boost::multiprecision`) aren't compatible with `constexpr`. I left these files untouched, but I'd be happy to add comments indicating why they don't have `BOOST_CONSTEXPR` but the others do. Just let me know.  
  
independent_bits_engine can be constexpr as long as the  
value type is a built-in type.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: swatanabe  
> Created_at: 2019-04-01 18:20:47 UTC  
> Url: https://github.com/boostorg/random/pull/52#issuecomment-478689089  

ed7ab807

---

## Comment 3

> Username: rtimmons  
> Created_at: 2019-04-01 18:25:39 UTC  
> Url: https://github.com/boostorg/random/pull/52#issuecomment-478690771  

Thanks for your help @swatanabe - I noticed you did the hard work here.  
  
You may consider incorporating the tests I added in f9ee70d24ed821a53486d441d4122f1420679ef7 but that's your call.   
  
I also noticed that you may want to add `BOOST_CONSTEXPR` in `indepdendent_bits::max_imp(false_type)` [here](https://github.com/boostorg/random/blob/ed7ab807e6f9c8e99f380cd21f5a00058b985d49/include/boost/random/independent_bits.hpp#L244-L253).

---
