# #2193 Version 311 - MSVC Fix and Extend MSVC tests [Merged]

> Username: madmongo1  
> Created at: 2021-03-15 23:29:17 UTC  
> Updated at: 2021-03-16 10:26:54 UTC  
> Merged at: 2021-03-16 10:26:54 UTC  
> Closed at: 2021-03-16 10:26:54 UTC  
> Url: https://github.com/boostorg/beast/pull/2193  



---

## Comment 1

> Username: sdarwin  
> Created_at: 2021-03-15 23:37:49 UTC  
> Url: https://github.com/boostorg/beast/pull/2193#issuecomment-799832538  

@madmongo1 ,  I just noticed something else:  
  
Immediately after each line of actual testing in the drone script, add this line, so the script will exit with an error.     That is, if the script runs 3 tests, this should  appear 3 times:  
```  
if %errorlevel% neq 0 exit /b %errorlevel%  
```

---

## Comment 2

> Username: madmongo1  
> Created_at: 2021-03-16 02:22:21 UTC  
> Url: https://github.com/boostorg/beast/pull/2193#issuecomment-799892940  

> @madmongo1 , I just noticed something else:  
>   
> Immediately after each line of actual testing in the drone script, add this line, so the script will exit with an error. That is, if the script runs 3 tests, this should appear 3 times:  
>   
> ```  
> if %errorlevel% neq 0 exit /b %errorlevel%  
> ```  
  
done on next push. thanks

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-16 04:13:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/2193#pullrequestreview-612838285  

📁 include/boost/beast/core/impl/multi_buffer.hpp

```diff
 918 |             if(in_size_ > altn)
 919 |-                 altn = std::numeric_limits<std::size_t>::max();
 919 |+                 altn = (std::numeric_limits<std::size_t>::max)();
```

> Username: vinniefalco  
> Created_at: 2021-03-16 04:13:06 UTC  
> Updated_at: 2021-03-16 09:04:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2193#discussion_r594848706  

Maybe do a search for `"max("` (and `"min("`) in all files?

> Username: madmongo1  
> Created_at: 2021-03-16 08:40:52 UTC  
> Updated_at: 2021-03-16 09:04:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2193#discussion_r594960692  

You must be looking at an old commit?  
I have   
```  
            if(in_size_ > altn)  
                altn = (std::numeric_limits<std::size_t>::max)();  
```

> Username: madmongo1  
> Created_at: 2021-03-16 08:42:20 UTC  
> Updated_at: 2021-03-16 09:04:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2193#discussion_r594961627  

Ah I see. it was just a comment.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-03-16 04:13:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2193#issuecomment-799932328  

This looks good, as long as there are no other occurrences

---
