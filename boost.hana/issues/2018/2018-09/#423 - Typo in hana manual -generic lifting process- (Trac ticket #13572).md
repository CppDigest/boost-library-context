# #423 - Typo in hana manual "generic lifting process" (Trac ticket #13572) [Closed]

> Username: mloskot  
> Created at: 2018-09-21 09:05:13 UTC  
> Updated at: 2018-09-22 04:55:33 UTC  
> Closed at: 2018-09-22 04:55:07 UTC  
> Url: https://github.com/boostorg/hana/issues/423  

(copied from https://svn.boost.org/trac10/ticket/13572 BTW, Trac seem to be missing component for `hana`)  
  
This page:  
https://www.boost.org/doc/libs/1_67_0/libs/hana/doc/html/index.html#tutorial-type-lifting  
contains:  
  
```  
return hana::type<T*>;  
```  
  
This fails clang compilation. When changed to:  
  
```  
return hana::type_c<T*>;  
```  
  
it passes clang compilation.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2018-09-21 15:06:25 UTC  
> Url: https://github.com/boostorg/hana/issues/423#issuecomment-423565420  

Perhaps we should change it to  
```cpp  
return hana::type<T*>{};  
```  
Louis uses that form in his slides claiming it was better for teaching newcomers.

---

## Comment 2

> Username: ldionne  
> Created at: 2018-09-22 04:53:49 UTC  
> Url: https://github.com/boostorg/hana/issues/423#issuecomment-423716779  

Thanks a lot for reporting -- I did not realize some people were filing bugs in Trac.

---

## Comment 3

> Username: ldionne  
> Created at: 2018-09-22 04:55:26 UTC  
> Updated at: 2018-09-22 04:55:33 UTC  
> Url: https://github.com/boostorg/hana/issues/423#issuecomment-423716837  

@ricejasonf I used `type_c` for consistency with the rest of the tutorial.
