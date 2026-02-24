# #26 Fixed name shadowing warnings [Merged]

> Username: Kojoley  
> Created at: 2016-09-01 16:20:11 UTC  
> Updated at: 2023-08-16 13:26:28 UTC  
> Merged at: 2023-08-16 13:26:28 UTC  
> Closed at: 2023-08-16 13:26:28 UTC  
> Url: https://github.com/boostorg/random/pull/26  

Fixes following warnings:  
  
```  
In file included from ../../../boost/random/ranlux.hpp:20:0,  
                 from ../../../boost/random.hpp:44,  
                 from qi/range_run.cpp:11:  
../../../boost/random/subtract_with_carry.hpp: In member function ‘IntType boost::random::subtract_with_carry_engine<IntType, w, s, r>::do_update(std::size_t, std::size_t, IntType)’:  
../../../boost/random/subtract_with_carry.hpp:272:5: warning: declaration of ‘carry’ shadows a member of ‘boost::random::subtract_with_carry_engine<IntType, w, s, r>’ [-Wshadow]  
     {  
     ^  
In file included from ../../../boost/random/ranlux.hpp:20:0,  
                 from ../../../boost/random.hpp:44,  
                 from qi/range_run.cpp:11:  
../../../boost/random/subtract_with_carry.hpp:305:13: note: shadowed declaration is here  
     IntType carry;  
             ^  
In file included from ../../../boost/random/ranlux.hpp:20:0,  
                 from ../../../boost/random.hpp:44,  
                 from qi/range_run.cpp:11:  
../../../boost/random/subtract_with_carry.hpp: In member function ‘RealType boost::random::subtract_with_carry_01_engine<RealType, w, s, r>::do_update(std::size_t, std::size_t, RealType)’:  
../../../boost/random/subtract_with_carry.hpp:502:5: warning: declaration of ‘carry’ shadows a member of ‘boost::random::subtract_with_carry_01_engine<RealType, w, s, r>’ [-Wshadow]  
     {  
     ^  
../../../boost/random/subtract_with_carry.hpp:515:14: note: shadowed declaration is here  
     RealType carry;  
              ^  
```  
  
```  
../../../boost/random/subtract_with_carry.hpp:247:62: warning: declaration shadows a field of 'subtract_with_carry_engine<IntType, w, s, r>' [-Wshadow]  
    friend bool operator==(const subtract_with_carry_engine& x, const subtract_with_carry_engine& y)  
                                                             ^  
../../../boost/random/subtract_with_carry.hpp:303:13: note: previous declaration is here  
    IntType x[long_lag];  
            ^  
../../../boost/random/subtract_with_carry.hpp:481:65: warning: declaration shadows a field of 'subtract_with_carry_01_engine<RealType, w, s, r>' [-Wshadow]  
    friend bool operator==(const subtract_with_carry_01_engine& x, const subtract_with_carry_01_engine& y)  
                                                                ^  
../../../boost/random/subtract_with_carry.hpp:516:14: note: previous declaration is here  
    RealType x[long_lag];  
             ^  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2017-12-26 12:45:44 UTC  
> Url: https://github.com/boostorg/random/pull/26#issuecomment-353964875  

If you prefer to change names of struct members just let me know it.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2019-12-06 09:18:45 UTC  
> Updated_at: 2019-12-06 09:19:36 UTC  
> Url: https://github.com/boostorg/random/pull/26#issuecomment-562496200  

Hi @swatanabe   
  
This patch was proposed 3 years ago, is there still any chance that it (or a variant of it) get's merged ?  
  
Cheers,  
Romain

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2020-06-08 10:21:52 UTC  
> Url: https://github.com/boostorg/random/pull/26#issuecomment-640514598  

Ping

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2022-12-12 16:39:49 UTC  
> Url: https://github.com/boostorg/random/pull/26#issuecomment-1346849746  

Ping

---

## Comment 5

> Username: Romain-Geissler-1A  
> Created_at: 2023-08-15 22:39:04 UTC  
> Url: https://github.com/boostorg/random/pull/26#issuecomment-1679719898  

@swatanabe Would it be possible to consider this pull request for review ?

---
