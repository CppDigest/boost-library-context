# #566 - C2131 expression did not evaluate to a constraint [Closed]

> Username: darkpotpot  
> Created at: 2023-09-14 08:40:09 UTC  
> Updated at: 2023-09-21 15:02:37 UTC  
> Closed at: 2023-09-21 15:02:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/566  

Hi,  
  
I'm currently updating my projects in order to use c++20 and I'm having trouble with one of them. It uses boost geometry (which uses boost multiprecision), and at some point in the compilation it failed with the following error:  
  
> C2131 expression did not evaluate to a constraint  
  
If I dig a little bit, the error comes from 2 files :  
  
- traits\max_digits10.hpp:  
    - l74 :    static constexpr std::size_t value = digits_10(digits);  
    - l44:    static constexpr std::size_t value = max_digits_10(digits);  
- cpp_int\limits.hpp:  
    - l188:    static constexpr int  digits10     = static_cast<int>(boost::multiprecision::detail::calc_digits10_s<static_cast<std::size_t>(digits)>::value);  
    - l189:    static constexpr int  max_digits10 = static_cast<int>(boost::multiprecision::detail::calc_max_digits10_s<static_cast<std::size_t>(digits)>::value);  
  
  
I'm using Visual studio 2022 with v142 "platform toolset" and ISO C++20 "language standard"  
Boost version is 1.83.0  
  
Does anybody know how I can get rid of the issue ?  
  
Regards  
  
Julien  
  
Edit : I wrongly suspected language standard. It fails with all. During the update I switched from boost 1.72 to 1.83 because of issues in c++20 with others boost modules. So in fact it came from this update,  
I tested several version and issue appears in 1.78.0. All release after this one shows the issue  
Version 1.77.0 and previous are running fine

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-09-15 16:30:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/566#issuecomment-1721550848  

That's strange, we obviously test with MSVC (and I use for development here), and I haven't seen that.  Do you have a reduced self-contained test case I can look at?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2023-09-15 16:54:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/566#issuecomment-1721579151  

> using Visual studio 2022 with v142  
  
This one thing struck me as a bit unconventional... 2022 ships with 143. I don't se _why_ this could be responsible for strange behavior but it is a non-standard config?

---

## Comment 3

> Username: darkpotpot  
> Created at: 2023-09-21 09:32:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/566#issuecomment-1729208555  

Thanks for the answer, I'll try reproducing it with a minimal example and post it here

---

## Comment 4

> Username: darkpotpot  
> Created at: 2023-09-21 15:02:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/566#issuecomment-1729765829  

Hm, culprit found. It  was caused by another 3rd party lib (APDFL). Sorry for the trouble
