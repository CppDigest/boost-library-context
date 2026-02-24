# #167 - Compiler warnings [Closed]

> Username: dschwen  
> Created at: 2024-04-19 15:15:53 UTC  
> Updated at: 2024-09-29 20:42:55 UTC  
> Closed at: 2024-09-29 19:51:17 UTC  
> Url: https://github.com/boostorg/parser/issues/167  

We're about to use this library in a big software project that has strict guidelines about compiler warnings. The parser library emits a bunch of `unused parameter` warnings. We could silence `-Wunused-parameter` for the parser includes, but you might be interested in cleaning those up.

---

## Comment 1

> Username: dschwen  
> Created at: 2024-04-22 17:10:09 UTC  
> Updated at: 2024-04-22 17:10:19 UTC  
> Url: https://github.com/boostorg/parser/issues/167#issuecomment-2070279403  

These `comparison is always false due to limited range of data type` warnings are a bit concerning. Looks like `curr_c` is a signed char, which cannot represent `0xe0` etc.  
  
```  
/home/schwd/Programs/moose-capability-registry/framework/contrib/boost/include/boost/parser/detail/text/transcode_iterator.hpp:2914:35: warning: comparison is always false due to limited range of data type [-Wtype-limits]  
 2914 |                 } else if (curr_c == 0xe0) {  
      |                            ~~~~~~~^~~~~~~  
/home/schwd/Programs/moose-capability-registry/framework/contrib/boost/include/boost/parser/detail/text/transcode_iterator.hpp:2946:35: warning: comparison is always false due to limited range of data type [-Wtype-limits]  
 2946 |                 } else if (curr_c == 0xed) {  
      |                            ~~~~~~~^~~~~~~  
/home/schwd/Programs/moose-capability-registry/framework/contrib/boost/include/boost/parser/detail/text/transcode_iterator.hpp:2979:35: warning: comparison is always false due to limited range of data type [-Wtype-limits]  
 2979 |                 } else if (curr_c == 0xf0) {  
      |                            ~~~~~~~^~~~~~~  
/home/schwd/Programs/moose-capability-registry/framework/contrib/boost/include/boost/parser/detail/text/transcode_iterator.hpp:3025:35: warning: comparison is always false due to limited range of data type [-Wtype-limits]  
 3025 |                 } else if (curr_c == 0xf4) {  
      |                            ~~~~~~~^~~~~~~  
```

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-09-29 20:42:54 UTC  
> Url: https://github.com/boostorg/parser/issues/167#issuecomment-2381595962  

Thanks for reporting this!
