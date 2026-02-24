# #286 - clang-15 warning: 'codecvt_utf8<wchar_t, 1114111, 0>' is deprecated [Closed]

> Username: k15tfu  
> Created at: 2022-12-21 22:40:07 UTC  
> Updated at: 2025-02-26 14:58:02 UTC  
> Closed at: 2025-02-26 14:58:00 UTC  
> Url: https://github.com/boostorg/process/issues/286  

Hi!  
  
In file boost/process/locale.hpp:  
```  
boost/process/locale.hpp:77:45: error: 'codecvt_utf8<wchar_t, 1114111, 0>' is deprecated [-Werror,-Wdeprecated-declarations]  
    return std::locale(global_loc, new std::codecvt_utf8<wchar_t>);  
                                            ^  
[...]  
1 error generated.  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-13 16:24:45 UTC  
> Url: https://github.com/boostorg/process/issues/286#issuecomment-1589642929  

Well, that got deprecate without a proper replacement. I recommend you use v2, because I don't know how a fix could look like.

---

## Comment 2

> Username: francoisk  
> Created at: 2025-02-07 11:27:48 UTC  
> Url: https://github.com/boostorg/process/issues/286#issuecomment-2642657658  

This is now an error under clang-18/libc++ on FreeBSD with -std=c++26 (`codecvt_utf8` was removed in C++26).  
  
```  
include/boost/process/v1/locale.hpp:79:45: error: no template named 'codecvt_utf8' in namespace 'std'  
   79 |     return std::locale(global_loc, new std::codecvt_utf8<wchar_t>);  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-02-26 14:58:00 UTC  
> Url: https://github.com/boostorg/process/issues/286#issuecomment-2685321050  

Yes. Use process.v2.
