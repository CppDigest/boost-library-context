# #197 - Build fails on Clang 3.7 [Closed]

> Username: pdimov  
> Created at: 2021-07-04 22:40:59 UTC  
> Updated at: 2021-07-04 23:10:13 UTC  
> Closed at: 2021-07-04 23:10:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/197  

```  
libs/filesystem/src/path.cpp:1185:27: error: default initialization of an object of const type 'const (anonymous namespace)::path_locale_deleter' without a user-provided default constructor  
const path_locale_deleter g_path_locale_deleter;  
                          ^  
                                               {}  
1 error generated.  
```  
https://github.com/boostorg/stacktrace/runs/2985274408?check_suite_focus=true

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-04 23:10:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/197#issuecomment-873680667  

Thanks, should be fixed in 97f93a8.
