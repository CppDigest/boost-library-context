# #332 Avoid unused constant error/warning [Closed]

> Username: Flamefire  
> Created at: 2025-04-05 08:48:57 UTC  
> Updated at: 2025-04-05 13:26:32 UTC  
> Closed at: 2025-04-05 12:30:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/332  

symloop_max is only used for POSIX_API and otherwise Clang warns about it:  
  
> unused variable 'symloop_max' [-Werror,-Wunused-const-variable]  
> BOOST_CONSTEXPR_OR_CONST unsigned int symloop_max =  
  
Move it into the define-check block

---
