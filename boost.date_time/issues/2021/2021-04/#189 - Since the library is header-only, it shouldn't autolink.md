# #189 - Since the library is header-only, it shouldn't autolink [Closed]

> Username: pdimov  
> Created at: 2021-04-28 15:36:33 UTC  
> Updated at: 2021-04-30 18:51:49 UTC  
> Closed at: 2021-04-30 18:51:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/189  

This part  
https://github.com/boostorg/date_time/blob/develop/include/boost/date_time/compiler_config.hpp#L126-L145  
probably needs to be removed now, because including a header causes `LNK1104: cannot open file ‘libboost_date_time-vc142-mt-x64-1_76.lib’` and this should no longer be necessary.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2021-04-29 00:41:25 UTC  
> Url: https://github.com/boostorg/date_time/issues/189#issuecomment-828870274  

Yeah, you're right -- although the error is odd since the library is still built -- I believe at your suggestion for backward compatibility....

---

## Comment 2

> Username: pdimov  
> Created at: 2021-04-29 00:45:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/189#issuecomment-828872015  

Yes, the library is still built if you build Boost, but not if you don't. :-) (ASIO f.ex. is header-only, but includes DateTime headers.)

---

## Comment 3

> Username: JeffGarland  
> Created at: 2021-04-29 01:33:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/189#issuecomment-828885474  

Good point.  Will remove...soon...

---

## Comment 4

> Username: JeffGarland  
> Created at: 2021-04-30 17:58:13 UTC  
> Url: https://github.com/boostorg/date_time/issues/189#issuecomment-830264317  

@pdimov do you think the dyn_link can be removed to -- or is there some dependence elsewhere?  
  
```  
// we need to import/export our code only if the user has specifically  
// asked for it by defining either BOOST_ALL_DYN_LINK if they want all boost  
// libraries to be dynamically linked, or BOOST_DATE_TIME_DYN_LINK  
// if they want just this one to be dynamically liked:  
#if defined(BOOST_ALL_DYN_LINK) || defined(BOOST_DATE_TIME_DYN_LINK)  
    // export if this is our own source, otherwise import:  
#   ifdef BOOST_DATE_TIME_SOURCE  
#     define BOOST_DATE_TIME_DECL BOOST_SYMBOL_EXPORT  
#   else  
#     define BOOST_DATE_TIME_DECL BOOST_SYMBOL_IMPORT  
#   endif  // BOOST_DATE_TIME_SOURCE  
#endif  // DYN_LINK  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2021-04-30 18:01:50 UTC  
> Url: https://github.com/boostorg/date_time/issues/189#issuecomment-830266376  

I wouldn't touch that part. If you don't have any exports the Windows linker doesn't generate a .lib file.
