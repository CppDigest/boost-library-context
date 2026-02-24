# #148 Correct docs for GCC's 'Q' suffix [Closed]

> Username: jwakely  
> Created at: 2019-07-31 14:02:35 UTC  
> Updated at: 2019-08-02 16:09:31 UTC  
> Closed at: 2019-08-02 16:09:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/148  

There is no 'operator""Q', it's a built-in numeric suffix for floating-point literals, not a user-defined literal operator. Support for that suffix is disabled for -std=c++NN modes unless you use the -fext-numeric-literals option to enable the extension.  
  
Also the correct spelling of the option is -std not --std.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-07-31 14:20:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/148#issuecomment-516869825  

Ah, I need to correct the source in multiprecision.qbk, I'll take care of that.

---

## Comment 2

> Username: jwakely  
> Created_at: 2019-07-31 14:27:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/148#issuecomment-516873033  

Oh sorry, I didn't realise that is a generated file.

---

## Comment 3

> Username: pabristow  
> Created_at: 2019-07-31 14:38:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/148#issuecomment-516878121  

Comments are still correct (and useful)!

---
