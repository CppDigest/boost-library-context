# #542 - X3 numeric parsers matching a num value [Open]

> Username: wmamrak  
> Created at: 2019-10-07 20:58:35 UTC  
> Updated at: 2025-05-10 01:27:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/542  

In X3, matching a num by numeric parsers is not possible because numeric parsers do not have operator(), despite the docs stating the opposite.  
https://www.boost.org/doc/libs/develop/libs/spirit/doc/x3/html/spirit_x3/quick_reference/numeric.html  
  
Please update the docs.

---

## Comment 1

> Username: djowel  
> Created at: 2019-10-08 01:25:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/542#issuecomment-539272507  

Thanks. PR appreciated!

---

## Comment 2

> Username: wmamrak  
> Created at: 2019-10-08 07:19:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/542#issuecomment-539379230  

Hello,  
Is it the final or temporary behaviour?

---

## Comment 3

> Username: djowel  
> Created at: 2019-10-08 14:06:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/542#issuecomment-539530274  

Probably final, unless someone wants to implement it.
