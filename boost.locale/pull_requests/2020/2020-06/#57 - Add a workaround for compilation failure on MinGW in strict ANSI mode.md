# #57 Add a workaround for compilation failure on MinGW in strict ANSI mode [Closed]

> Username: Lastique  
> Created at: 2020-06-07 15:57:21 UTC  
> Updated at: 2022-05-24 14:28:44 UTC  
> Closed at: 2022-05-24 13:27:18 UTC  
> Url: https://github.com/boostorg/locale/pull/57  

When GNU extensions are disabled, `stdio.h` in legacy MinGW does not declare non-standard wide character functions, including `_wfopen`. Use the standard `fopen` in that case.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-05-24 13:27:18 UTC  
> Url: https://github.com/boostorg/locale/pull/57#issuecomment-1135923938  

I got a better solution which I also use in Nowide: https://github.com/Flamefire/locale/commit/9dacae00caeaf1b1004ba073adfdc17327e63357  
Will merge that one once CI is set up and this failure is reproduced (it does locally)

---
