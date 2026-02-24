# #89 - tools/config.hpp fixes [Closed]

> Username: smr99  
> Created at: 2017-09-26 02:22:31 UTC  
> Updated at: 2018-04-22 17:18:39 UTC  
> Closed at: 2018-04-22 17:18:39 UTC  
> Url: https://github.com/boostorg/math/issues/89  

Hi,   
  
Two items.  
  
1. Commit cc8e300 commented out the following define in tools/config.hpp:  
```  
#if (defined(__CYGWIN__) || defined(__FreeBSD__) || defined(__NetBSD__) \  
   || (defined(__hppa) && !defined(__OpenBSD__)) || (defined(__NO_LONG_DOUBLE_MATH) && (DBL_MANT_DIG != LDBL_MANT_DIG))) \  
   && !defined(BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS)  
//#  define BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
#endif  
  
```  
To me, this appears to be an error.    
  
  
2. Debian linux for hppa has been shipping with a patch for this condition.  Specifically to exclude linux in this situation:  
  
 ```  
#if (defined(__CYGWIN__) || defined(__FreeBSD__) || defined(__NetBSD__) \  
   || (defined(__hppa) && !defined(__OpenBSD__) && !defined(__linux__)) || (defined(__NO_LONG_DOUBLE_MATH) && (DBL_MANT_DIG != LDBL_MANT_DIG))) \  
   && !defined(BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS)  
  
...  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-04-22 17:18:39 UTC  
> Url: https://github.com/boostorg/math/issues/89#issuecomment-383397369  

It was a mistake for sure, fixed in develop.  
  
Thanks for the report!
