# #90 - Require C++11 in Boost 1.81 [Closed]

> Username: Flamefire  
> Created at: 2022-06-02 16:22:34 UTC  
> Updated at: 2022-09-14 14:54:48 UTC  
> Closed at: 2022-09-14 14:54:48 UTC  
> Url: https://github.com/boostorg/locale/issues/90  

It might be worth dropping support for C++98. In some PRs move-support was requested and some code places may benefit from defaulted functions.  
So C++11 features I'd like to use:  
- Rvalue references (move-assign/construct where useful)  
- `std::unique_ptr`, dropping std::auto_ptr which removes a ton of warnings  
- defaulted functions  
  
Nothing really big but keeping support for C++98 makes the code harder to write and read due to ifdef-macros etc.  
Additionally ICU now requires C++11 as they use `char16_t` etc. in some of their headers. So building Boost.Locale against such an ICU version requires C++11 anyway.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2022-06-03 10:39:53 UTC  
> Url: https://github.com/boostorg/locale/issues/90#issuecomment-1145832743  

Back than it was important to keep C++98/03 support, however today, 11 years after C++11 was released I think dropping C++03 is Ok, especially if it simplifies maintenance.  
  
Finally today, if keeping C++03 support becomes harder than just drop it.  
  
> Additionally ICU now requires C++11 as they use `char16_t`  
  
Irony. I wish they did something in C++ ish way (and not Java written in C++)... Now they require C++11.
