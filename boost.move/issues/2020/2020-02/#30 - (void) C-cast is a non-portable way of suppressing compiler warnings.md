# #30 - (void) C-cast is a non-portable way of suppressing compiler warnings [Closed]

> Username: fearsomepirate  
> Created at: 2020-02-05 20:30:51 UTC  
> Updated at: 2020-10-20 09:29:47 UTC  
> Closed at: 2020-10-20 09:29:25 UTC  
> Url: https://github.com/boostorg/move/issues/30  

in move/algo/detail/merge.cpp, lines 683 and 720, it looks like a (void) C-style cast is being used to suppress unused variable warnings in the compiler. This is not part of the standards and doesn't work for EDG-based compilers like Intel (doesn't work as of at least Intel 18.0), which renders some things unusable if you're using Intel and have compiler warnings handled as errors.  
  
https://herbsutter.com/2009/10/18/mailbag-shutting-up-compiler-warnings/#comment-1509  
  
boost::ignore_unused provides this functionality in a portable way. See boost/core/ignore_unused.hpp.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-10-20 09:29:47 UTC  
> Url: https://github.com/boostorg/move/issues/30#issuecomment-712721401  

Many thanks for the report and sorry for the long time to fix it.
