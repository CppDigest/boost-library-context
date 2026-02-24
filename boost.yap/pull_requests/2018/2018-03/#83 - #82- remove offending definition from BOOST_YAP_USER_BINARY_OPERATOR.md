# #83 #82: remove offending definition from BOOST_YAP_USER_BINARY_OPERATOR [Closed]

> Username: rgrover  
> Created at: 2018-03-19 05:09:32 UTC  
> Updated at: 2018-06-11 17:16:21 UTC  
> Closed at: 2018-03-22 03:20:21 UTC  
> Url: https://github.com/boostorg/yap/pull/83  

Not sure how you want to handle this, but removing the offending definition is a quick workaround.

---

## Comment 1

> Username: tzlaine  
> Created_at: 2018-03-22 03:17:22 UTC  
> Updated_at: 2018-03-22 03:18:10 UTC  
> Url: https://github.com/boostorg/yap/pull/83#issuecomment-375166248  

I don't get it.  What is being fixed?  Note that this fix does not work (or at least not cleanly), as it breaks the tests.

---

## Comment 2

> Username: tzlaine  
> Created_at: 2018-03-22 03:20:21 UTC  
> Url: https://github.com/boostorg/yap/pull/83#issuecomment-375166698  

Nevermind, I see the ticket now.  I guess I hadn't refreshed the issues page in my browser in a while.  Even so, this won't work as the permanent fix.  Closing.

---

## Comment 3

> Username: rgrover  
> Created_at: 2018-03-22 03:20:37 UTC  
> Url: https://github.com/boostorg/yap/pull/83#issuecomment-375166737  

refer to https://github.com/tzlaine/yap/issues/82  
  
Add the following to some code where expr1 and expr2 are expression templates.  
```C++  
BOOST_YAP_USER_BINARY_OPERATOR(multiplies, expr1, resulting_expr)  
BOOST_YAP_USER_BINARY_OPERATOR(multiplies, expr2, resulting_expr)  
```  
  
The compiler will complain about duplicate definition for operator `multiplies` because one of the definitions arising from the use of macro doesn't depend on the source expression.

---
