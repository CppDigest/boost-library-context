# #66 - Construct from std::string_view [Closed]

> Username: cos-public  
> Created at: 2018-08-15 11:10:15 UTC  
> Updated at: 2018-08-18 09:41:01 UTC  
> Closed at: 2018-08-18 09:41:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/66  

Would be handy. And from IEEE-754 (https://en.wikipedia.org/wiki/IEEE_754) decimals as well.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2018-08-15 18:41:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/66#issuecomment-413294874  

I like the ideas, especiall potentially creating cpp_dec_float from IEEE-754 decimal type. At the moment, I do not have any experience with IEE-754 decimals, other than being vaguely aware of their specification. Thanks for the ideas, Chris.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-16 10:36:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/66#issuecomment-413500853  

Waiting on https://github.com/boostorg/config/pull/239 before I can configure this.  Fortunately I know the library's author ;)  
  
Re libDecimal: it's been on our TODO list from the start.  Hadn't realised that GCC supported the decimal number TR directly, will look into that.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-08-18 09:41:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/66#issuecomment-414045627  

The string_view support is now in develop.  
  
Closing as fixed, I've opened separate issues for decimal number support:  
  
https://github.com/boostorg/multiprecision/issues/73  
https://github.com/boostorg/multiprecision/issues/74
