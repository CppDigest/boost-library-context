# #735 - How to include preprocessor directives in BOOST_COMPUTE_STRINGIZE_SOURCE [Closed]

> Username: HolyWu  
> Created at: 2017-07-24 14:58:19 UTC  
> Updated at: 2017-07-25 00:52:19 UTC  
> Closed at: 2017-07-25 00:52:19 UTC  
> Url: https://github.com/boostorg/compute/issues/735  

I want to use #if or #ifdef in the OpenCL source code. But the macro won't receive the preprocessor directives and stringize them since the directives will be resolved first by the preprocessor before compilation begins. Is there any way to solve it? Thanks.

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-07-24 16:58:21 UTC  
> Url: https://github.com/boostorg/compute/issues/735#issuecomment-317486239  

If you want keep using macro, then no. However, you can just put your code in a string.

---

## Comment 2

> Username: jszuppe  
> Created at: 2017-07-24 17:00:27 UTC  
> Url: https://github.com/boostorg/compute/issues/735#issuecomment-317486850  

I'd recommend using raw string literal.

---

## Comment 3

> Username: HolyWu  
> Created at: 2017-07-25 00:52:19 UTC  
> Url: https://github.com/boostorg/compute/issues/735#issuecomment-317596194  

I see, thanks.
