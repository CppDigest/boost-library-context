# #12 - BoostBook outputs H7 headings displayed with link styling [Closed]

> Username: evanlenz  
> Created at: 2022-02-05 20:11:00 UTC  
> Updated at: 2022-05-05 23:56:56 UTC  
> Closed at: 2022-02-28 22:26:36 UTC  
> Url: https://github.com/boostorg/boostbook/issues/12  

Example:  
https://www.boost.org/doc/libs/1_78_0/libs/json/doc/html/json/ref/boost__json__value/swap/overload1.html  
  
![image](https://user-images.githubusercontent.com/604974/152657178-006a8e53-bb12-4595-82e3-8f1d053e7e93.png)  
  
The headings (Synopsis, Description, Complexity, Exception Safety, and Parameters) all are displayed with link styling (blue text, underline-on-hover, etc.). This is because boostbook.css only handles the valid HTML heading elements (up to H6). The imported DocBook stylesheets, however, can sometimes output H7.

---

## Comment 1

> Username: evanlenz  
> Created at: 2022-02-05 20:19:56 UTC  
> Updated at: 2022-02-05 20:20:10 UTC  
> Url: https://github.com/boostorg/boostbook/issues/12#issuecomment-1030692640  

I will submit a proposed fix that forces `<h7>` and higher to be rewritten as `<h6>`. That way, the HTML will be valid, and the CSS won't have to change.

---

## Comment 2

> Username: evanlenz  
> Created at: 2022-05-05 16:35:32 UTC  
> Url: https://github.com/boostorg/boostbook/issues/12#issuecomment-1118785757  

@glenfe Do you know if this got integrated into the build? When should we see the effects of it in the Boost release? It doesn't appear to have taken effect in the Boost 1.79.0 release. The same issue is reflected here: https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/ref/boost__json__value/swap/overload1.html  
  
Any ideas?

---

## Comment 3

> Username: glenfe  
> Created at: 2022-05-05 23:56:56 UTC  
> Url: https://github.com/boostorg/boostbook/issues/12#issuecomment-1119148593  

@evanlenz I forgot to merge it to master for 1.79.0. I'll make sure it's merged for 1.80.0
