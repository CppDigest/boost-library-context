# #17 Add missing include [Closed]

> Username: apolukhin  
> Created at: 2021-06-09 18:56:19 UTC  
> Updated at: 2021-06-09 18:57:08 UTC  
> Closed at: 2021-06-09 18:57:08 UTC  
> Url: https://github.com/boostorg/inspect/pull/17  

Compilation fails without the include:  
```  
 tools/inspect/ascii_check.cpp:89:46: error: ‘find_if’ is not a member of ‘std’; did you mean ‘find’?  
   89 |       string::const_iterator bad_char = std::find_if ( contents.begin (), contents.end (), non_ascii ());  
      |                                              ^~~~~~~  
      |                                              find  
tools/inspect/ascii_check.cpp:93:23: error: ‘count’ is not a member of ‘std’; did you mean ‘cout’?  
   93 |         int ln = std::count( contents.begin(), bad_char, '\n' ) + 1;  
      |                       ^~~~~  
      |                       cout  
  
    "g++"   -m64 -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_HAS_ICU=1  -I"." -I"/usr/include"  -c -o "bin.v2/tools/inspect/build/gcc-9/debug/link-static/ascii_check.o" "tools/inspect/ascii_check.cpp"  
```

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-06-09 18:57:08 UTC  
> Url: https://github.com/boostorg/inspect/pull/17#issuecomment-857990111  

Oops. this is a dup of #16

---
