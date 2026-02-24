# #19 Identity result_of [Closed]

> Username: denzor200  
> Created at: 2022-01-12 05:28:11 UTC  
> Updated at: 2022-01-12 06:56:24 UTC  
> Closed at: 2022-01-12 06:56:24 UTC  
> Url: https://github.com/boostorg/functional/pull/19  

Added the ability to use `boost::result_of` metafunction with `boost::identity` functional object types, for example:  
```  
typedef boost::result_of< boost::identity(int&) >::type type00;  
typedef boost::result_of< boost::identity(const int&) >::type type01;  
// etc..  
```  
This ability required by **Boost Fusion** side, and this PR need  to me to close this PR: https://github.com/boostorg/fusion/pull/240

---

## Comment 1

> Username: glenfe  
> Created_at: 2022-01-12 06:42:40 UTC  
> Updated_at: 2022-01-12 06:48:12 UTC  
> Url: https://github.com/boostorg/functional/pull/19#issuecomment-1010690669  

See https://github.com/boostorg/functional/commit/075c2e089a50d6a2f566d0415c62aa9a6e09f765

---

## Comment 2

> Username: denzor200  
> Created_at: 2022-01-12 06:56:24 UTC  
> Url: https://github.com/boostorg/functional/pull/19#issuecomment-1010699896  

@glenfe Thanks!

---
