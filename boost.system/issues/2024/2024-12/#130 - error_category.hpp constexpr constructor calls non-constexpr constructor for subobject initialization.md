# #130 - error_category.hpp constexpr constructor calls non-constexpr constructor for subobject initialization [Closed]

> Username: etiennearnal  
> Created at: 2024-12-09 17:12:25 UTC  
> Updated at: 2024-12-10 08:29:12 UTC  
> Closed at: 2024-12-10 08:29:11 UTC  
> Url: https://github.com/boostorg/system/issues/130  

Hi all,  
  
I'm building my app with GCC12.3 and C++20 and I include boost 1.86.  
I get this compilation error in `boost/system/detail/error_category.hpp`:  
  
```cpp  
In file included from boost/system/error_category.hpp(10),  
                 from boost/filesystem/detail/path_traits.hpp(26),  
                 from boost/filesystem/path.hpp(34),  
                 from boost/filesystem.hpp(16)  
boost/system/detail/error_category.hpp(87): error: constexpr constructor calls non-constexpr constructor for subobject initialization  
      constexpr error_category() noexcept: id_( 0 ), stdcat_(), sc_init_()  
                                                                        ^  
```  
  
My full compilation options are ` -fp-model=precise -diag-disable=10441 -g -fdata-sections -ffunction-sections -pthread -fopenmp -fpic -Wall -Werror -Wl,--start-group -O3 -DNDEBUG -DNDEBUG -std=gnu++20 -fPIE -fvisibility=hidden -MD -MT `.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-12-09 17:20:42 UTC  
> Url: https://github.com/boostorg/system/issues/130#issuecomment-2528800685  

Can you reproduce this on Compiler Explorer somehow? https://godbolt.org/z/GcMWs6oWa gives no errors.  
  
`sc_init_` is of type `std::atomic<unsigned>`  
  
https://github.com/boostorg/system/blob/09f4eb87e21069d7d032acad62ebb30694fc8d5c/include/boost/system/detail/error_category.hpp#L81C13-L81C36  
  
and the default constructor of `std::atomic` is supposed to be `constexpr` per the standard.  
  
`-diag-disable` is an Intel option; maybe this is an Intel-specific issue?

---

## Comment 2

> Username: etiennearnal  
> Created at: 2024-12-09 17:27:24 UTC  
> Url: https://github.com/boostorg/system/issues/130#issuecomment-2528815399  

According to the atomic header in my gcc 12.3 installation, atomic default contructor is not `constexpr`... 🤯  
![Image](https://github.com/user-attachments/assets/94bc7f9c-f0fd-4a89-9c7a-56f4e986e17e)

---

## Comment 3

> Username: pdimov  
> Created at: 2024-12-09 17:36:30 UTC  
> Url: https://github.com/boostorg/system/issues/130#issuecomment-2528843463  

Well, it works under GCC 12.3, which means that `= default` there does make it implicitly `constexpr`. As I said, if you're using Intel C++, it could be some difference in behavior specific to that compiler.  
  
You might try changing `sc_init_()` to `sc_init_(0)` and see if that helps; the constructor from `__integral_type` is explicitly marked `constexpr`.

---

## Comment 4

> Username: etiennearnal  
> Created at: 2024-12-10 08:29:11 UTC  
> Url: https://github.com/boostorg/system/issues/130#issuecomment-2530790585  

it works when I specify  `sc_init(0)`. I think that my Intel Compiler version (2023.1) doesn't support C++20 totally... Thanks for your help @pdimov .
