# #248 - Most of BOOST_HAS_TR1_* macros are ghosts [Closed]

> Username: Kojoley  
> Created at: 2018-10-24 19:18:39 UTC  
> Updated at: 2018-10-24 19:19:44 UTC  
> Closed at: 2018-10-24 19:19:44 UTC  
> Url: https://github.com/boostorg/config/issues/248  

I was going to use `BOOST_HAS_TR1_FUNCTION` to detect `std::function` availability, but find out that most of BOOST_HAS_TR1_* macros presented only in docs (17 documented and only 6 have any traces in the code).  
  
```  
#define BOOST_HAS_TR1_HASH  
#define BOOST_HAS_TR1_TYPE_TRAITS  
#define BOOST_HAS_TR1_UNORDERED_MAP  
#define BOOST_HAS_TR1_UNORDERED_SET  
#define BOOST_HAS_TR1_COMPLEX_INVERSE_TRIG  
#define BOOST_HAS_TR1_COMPLEX_OVERLOADS  
```  
  
Am I missing something?

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-24 19:19:43 UTC  
> Url: https://github.com/boostorg/config/issues/248#issuecomment-432793935  

Nevermind, I am blind :)
