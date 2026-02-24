# #166 Fix unused variable warnings [Merged]

> Username: chrisse74  
> Created at: 2021-11-09 15:31:56 UTC  
> Updated at: 2022-12-02 17:05:14 UTC  
> Merged at: 2022-12-02 17:05:14 UTC  
> Closed at: 2022-12-02 17:05:14 UTC  
> Url: https://github.com/boostorg/regex/pull/166  

Will fix the following unused variable warnings:  
  
```  
In file included from Boost/1.77.0/include/boost/regex/icu.hpp:27:  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:194:33: warning: unused variable 'mask_ascii' [-Wunused-variable]  
      constexpr char_class_type mask_ascii = char_class_type(1) << offset_ascii;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:193:33: warning: unused variable 'mask_any' [-Wunused-variable]  
      constexpr char_class_type mask_any = char_class_type(1) << offset_any;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:370:33: warning: unused variable 'mask_xdigit' [-Wunused-variable]  
      constexpr char_class_type mask_xdigit = char_class_type(1) << offset_xdigit;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:371:33: warning: unused variable 'mask_underscore' [-Wunused-variable]  
      constexpr char_class_type mask_underscore = char_class_type(1) << offset_underscore;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:376:33: warning: unused variable 'mask_vertical' [-Wunused-variable]  
      constexpr char_class_type mask_vertical = char_class_type(1) << offset_vertical;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:369:33: warning: unused variable 'mask_space' [-Wunused-variable]  
      constexpr char_class_type mask_space = char_class_type(1) << offset_space;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:372:33: warning: unused variable 'mask_unicode' [-Wunused-variable]  
      constexpr char_class_type mask_unicode = char_class_type(1) << offset_unicode;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:375:33: warning: unused variable 'mask_horizontal' [-Wunused-variable]  
      constexpr char_class_type mask_horizontal = char_class_type(1) << offset_horizontal;  
                                ^  
Boost/1.77.0/include/boost/regex/v5/icu.hpp:368:33: warning: unused variable 'mask_blank' [-Wunused-variable]  
      constexpr char_class_type mask_blank = char_class_type(1) << offset_blank;  
```

---

## Comment 1

> Username: chrisse74  
> Created_at: 2022-05-13 08:54:45 UTC  
> Url: https://github.com/boostorg/regex/pull/166#issuecomment-1125813716  

@jzmaddock any chance these 'unused variable warnings' could get fixed? :)

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2022-12-02 14:51:46 UTC  
> Url: https://github.com/boostorg/regex/pull/166#issuecomment-1335363013  

Ping

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-12-02 17:05:09 UTC  
> Url: https://github.com/boostorg/regex/pull/166#issuecomment-1335544541  

Sorry, yes that does actually look correct, will merge now.

---
