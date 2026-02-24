# #30 - How to unpack a sequence? [Closed]

> Username: hirrolot  
> Created at: 2020-05-23 13:20:45 UTC  
> Updated at: 2020-05-23 18:20:52 UTC  
> Closed at: 2020-05-23 18:20:52 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/30  

I want `(a)(b)(c)` -> `a b c`. Is there a library function for such a problem?

---

## Comment 1

> Username: eldiener  
> Created at: 2020-05-23 18:19:16 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/30#issuecomment-633110423  

The construct `(a)(b)(c)` is a 'seq'. You can use BOOST_PP_SEQ_FOR_EACH to manipulate the elements of a 'seq' as you like.

---

## Comment 2

> Username: hirrolot  
> Created at: 2020-05-23 18:20:52 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/30#issuecomment-633110710  

Alright, thank you.
