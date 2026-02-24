# #306 - "Phoenix" links are broken in documentation [Closed]

> Username: octopus-prime  
> Created at: 2017-12-01 15:37:39 UTC  
> Updated at: 2017-12-08 11:45:32 UTC  
> Closed at: 2017-12-08 11:45:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/306  

Starting here  
http://www.boost.org/doc/libs/1_65_1/libs/spirit/doc/html/spirit/qi/reference/auxiliary/lazy.html  
and clicking the `Phoenix` link you end up with `404`.  
  
The `Phoenix` link is  
http://www.boost.org/doc/libs/1_65_1/libs/spirit/phoenix/doc/html/index.html  
but should be  
http://www.boost.org/doc/libs/1_65_1/libs/phoenix/doc/html/index.html

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-12-05 11:49:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/306#issuecomment-349281498  

Same problem here  
http://www.boost.org/doc/libs/1_65_1/libs/spirit/doc/html/spirit/qi/reference/parser_concepts/nonterminal.html  
  
So probably all "Phoenix" links are broken.

---

## Comment 2

> Username: djowel  
> Created at: 2017-12-05 16:02:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/306#issuecomment-349350293  

Oh no! What could have happened?

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-12-06 19:35:12 UTC  
> Updated at: 2017-12-06 19:37:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/306#issuecomment-349750306  

In `spirit2.qbk` are two definitions for "Phoenix":  
```  
[def __phoenix__                [@../../phoenix/doc/html/index.html Phoenix]]  
[def __boost_phoenix__          [@boost:/libs/phoenix/doc/html/index.html Boost.Phoenix]]  
[def __fusion__                 [@boost:/libs/fusion/doc/html/index.html Boost.Fusion]]  
```  
The latter seams to be correct but the former is used everywhere. E.g.  
```  
The `lazy` parser, as its name suggests, invokes a lazy __phoenix__  
function that returns a parser at parse time. This parser will be  
used once it is created to continue the parse.  
```  
So the fix could be  
```  
[def __phoenix__                [@boost:/libs/phoenix/doc/html/index.html Boost.Phoenix]]  
[def __fusion__                 [@boost:/libs/fusion/doc/html/index.html Boost.Fusion]]  
```
