# #103 Syntactic change silences latest gcc warnings [Closed]

> Username: eldiener  
> Created at: 2016-09-16 22:49:35 UTC  
> Updated at: 2016-11-01 08:57:34 UTC  
> Closed at: 2016-10-18 21:44:44 UTC  
> Url: https://github.com/boostorg/test/pull/103  

Gcc issues warnings whenever it encounters the expansion of the macro, claiming the 'if' statements could be confusing. This purely syntactical change of removing the 'if' one liners in favor of the 'if' block silences the warnings.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2016-10-18 21:44:44 UTC  
> Url: https://github.com/boostorg/test/pull/103#issuecomment-254648631  

Merged to PR/remove-gcc6-warnings

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2016-11-01 08:57:34 UTC  
> Url: https://github.com/boostorg/test/pull/103#issuecomment-257517533  

In develop

---
