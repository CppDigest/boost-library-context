# #88 - List of non BOOST-prefixed macros [Closed]

> Username: ned14  
> Created at: 2019-12-16 13:48:14 UTC  
> Updated at: 2026-02-19 20:12:51 UTC  
> Closed at: 2026-02-19 20:12:51 UTC  
> Url: https://github.com/boostorg/program_options/issues/88  

The following macros are missing a BOOST_ prefix, which is against Boost library guidelines:  
  
```  
./boost/program_options.hpp:#ifndef PROGRAM_OPTIONS_VP_2003_05_19  
./boost/program_options.hpp:#define PROGRAM_OPTIONS_VP_2003_05_19  
./boost/program_options/config.hpp:#endif // PROGRAM_OPTIONS_CONFIG_HK_2004_01_11  
```

---

## Comment 1

> Username: vprus  
> Created at: 2019-12-17 15:19:05 UTC  
> Url: https://github.com/boostorg/program_options/issues/88#issuecomment-566586768  

Would you mind preparing a PR?

---

## Comment 2

> Username: ned14  
> Created at: 2019-12-17 15:46:22 UTC  
> Url: https://github.com/boostorg/program_options/issues/88#issuecomment-566598954  

Unfortunately I am not the one doing the work, I merely uploaded the results of another's work. Also, I cannot contribute work-related code without going through Legal first.

---

## Comment 3

> Username: vprus  
> Created at: 2019-12-17 16:10:57 UTC  
> Url: https://github.com/boostorg/program_options/issues/88#issuecomment-566622825  

Ok. While I might tweak this myself, the priority of the cosmetics that did not cause any real issues for 16 years is pretty low.
