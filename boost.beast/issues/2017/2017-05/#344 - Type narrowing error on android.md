# #344 - Type narrowing error on android [-Wc++11-narrowing] [Closed]

> Username: eakraly  
> Created at: 2017-05-07 11:39:03 UTC  
> Updated at: 2017-05-08 00:04:00 UTC  
> Closed at: 2017-05-08 00:04:00 UTC  
> Url: https://github.com/boostorg/beast/issues/344  

Compiling beast 1.0.0-b36 using `LOCAL_CPPFLAGS += -std=c++14 -Wall` with android NDK r14b gets this:  
  
>   
\~/beast/http/detail/rfc7230.hpp:241:9: error: constant expression evaluates to -1 which cannot be narrowed to type 'char' [-Wc++11-narrowing]  
        -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 0  
        ^\~  
  
  
\~/beast/http/detail/rfc7230.hpp:241:9: note: insert an explicit cast to silence this issue  
        -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 0  
        ^\~  
        static_cast<char>( )  
>

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-07 12:44:45 UTC  
> Url: https://github.com/boostorg/beast/issues/344#issuecomment-299704067  

Thanks for the report, the fix will go in to **b37**, would you consider reviewing the pull request and commenting on anything you want?  
https://github.com/vinniefalco/Beast/pull/341

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-07 17:50:09 UTC  
> Url: https://github.com/boostorg/beast/issues/344#issuecomment-299722495  

I see, it looks like your build environment has `char` as unsigned! I have added the compiler switch `-funsigned-char` to one of the CI build targets so I can reproduce your issue (and be notified if it ever happens again after I fix it!)
