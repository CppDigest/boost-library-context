# #34 - -Wsign-compare warning [Closed]

> Username: breese  
> Created at: 2017-11-18 15:17:44 UTC  
> Updated at: 2017-11-23 16:50:47 UTC  
> Closed at: 2017-11-23 16:50:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/34  

Compiling the first example under "Getting Started" with `-Wall` (gcc 5.4.0) results in `-Wsign-compare` warnings in the following line in `save_radd()`:  
```  
  if ((std::numeric_limits<T>::max() - t) < u)  
```  
These warnings are false positives, but it would be nice to get rid of them as they are quite noisy.  
  
The warning occurs because the minus operator turns its intermediary result into `int` (via integral promotion and arithmetic conversion) regardless of the sign of `T`.  
  
A workaround is to explicitly turn the intermediary result back into `T` because we know that max - t will never be negative.  
```  
  if (T(std::numeric_limits<T>::max() - t) < u)  
```  
Notice that we have to use the normal constructor `T(x)` instead of brace initialization `T{x}` because the latter would result in a narrowing warning, which btw is also a false positive here.  
  
PS: I am too lazy to fork-branch-commit-etc to do a pull request for this small change.

---

## Comment 1

> Username: HDembinski  
> Created at: 2017-11-20 10:23:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/34#issuecomment-345653490  

I used a static_cast<T> instead of T(...) which looks like a C-style cast. I hope this fixes the issue, thanks for reporting 👍

---

## Comment 2

> Username: HDembinski  
> Created at: 2017-11-20 10:26:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/34#issuecomment-345654170  

PS: Yeah, I wouldn't have made a PR for that either. It would be great if github supported small hot-fixes like this without requiring one to make a full PR.
