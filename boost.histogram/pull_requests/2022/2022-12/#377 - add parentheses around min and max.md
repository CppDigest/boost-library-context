# #377 add parentheses around min and max [Merged]

> Username: jhcarl0814  
> Created at: 2022-12-24 10:25:47 UTC  
> Updated at: 2022-12-24 21:30:47 UTC  
> Merged at: 2022-12-24 13:26:14 UTC  
> Closed at: 2022-12-24 13:26:14 UTC  
> Url: https://github.com/boostorg/histogram/pull/377  

add parentheses around `min` and `max` to suppress macro invocation  
https://stackoverflow.com/questions/6884093/warning-c4003-not-enough-actual-parameters-for-macro-max-visual-studio-2010  
https://stackoverflow.com/questions/7449620/how-to-call-stdmin-when-min-has-been-defined-as-a-macro  
https://stackoverflow.com/questions/22744262/cant-call-stdmax-because-minwindef-h-defines-max  
https://stackoverflow.com/questions/11544073/how-do-i-deal-with-the-max-macro-in-windows-h-colliding-with-max-in-std

---

## Comment 1

> Username: HDembinski  
> Created_at: 2022-12-24 12:14:06 UTC  
> Url: https://github.com/boostorg/histogram/pull/377#issuecomment-1364520363  

Thanks for this. Did you just spot these or did you use an algorithm to find all cases of unprotected min/max?

---

## Comment 2

> Username: HDembinski  
> Created_at: 2022-12-24 13:26:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/377#issuecomment-1364530168  

Thanks, these look all good. It is rediculous that Windows enforces this workaround on everyone.  
  
I don't like sprinkling the code with parenthesis because the reason is not clear unless you know about the Windows bug, but the only more obvious fix is incredibly verbose, so it is better this way. It is the technique used by Boost.Config. In `max(...)`, one insert  BOOST_PREVENT_MACRO_SUBSTITUATION between `max` and `(...)`, like so:  
```c++  
        ct width = max BOOST_PREVENT_MACRO_SUBSTITUTION (ct(1),  
```

---

## Comment 3

> Username: jhcarl0814  
> Created_at: 2022-12-24 21:30:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/377#issuecomment-1364585928  

@HDembinski Thank you for pointing out the standard way of doing the workaround.  
  
I was copying the test code into the beginning of `main` of an existing project (wanted to use IDE debugger) when fixing the bugs of the PR. There were some other test code for other stuffs that caused me to `#include<windows.h>` in the past, that's how I got the min and max errors.  
  
So I global searched `min(` and `max(` and added the parentheses. (Oh no! I missed the cases where there might be white spaces and comments between the identifier and parenthesis!)  
(I agree that the windows.h's macro is really annoying.)

---
