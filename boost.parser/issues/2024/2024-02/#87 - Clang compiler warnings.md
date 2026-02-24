# #87 - Clang compiler warnings [Closed]

> Username: akrzemi1  
> Created at: 2024-02-01 20:11:24 UTC  
> Updated at: 2024-02-03 12:35:14 UTC  
> Closed at: 2024-02-01 23:34:01 UTC  
> Url: https://github.com/boostorg/parser/issues/87  

The usage of "expectation" operator and and a sequence operator in one parser causes warnings to be displayed about the operator precedence:  
  
https://godbolt.org/z/xxGc8jod3  
  
Even without `-Wall`.   
I know that it is difficult to do something about it. Maybe clang should be contacted to stop warning about the operator precedence for user provided operators? Or parser classes need to be tagged in a way clang understands.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-01 23:34:02 UTC  
> Url: https://github.com/boostorg/parser/issues/87#issuecomment-1922481459  

There's nothing I can do about this.  You can parenthesize the expression however you like, and it will not change its meaning.  You can also push/pop warning disablements.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-03 12:35:13 UTC  
> Url: https://github.com/boostorg/parser/issues/87#issuecomment-1925309667  

FYI:  
https://github.com/llvm/llvm-project/issues/80538
