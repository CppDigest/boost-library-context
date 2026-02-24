# #518 - How to match the same char? [Closed]

> Username: w4454962  
> Created at: 2019-06-22 16:42:58 UTC  
> Updated at: 2019-06-22 22:07:34 UTC  
> Closed at: 2019-06-22 22:07:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/518  

one = ch_p("1") | ch_p("2")  | ch_p("3") ;  
  
two = one >> one;    = 12 13 21 23 31 32   
How can I  match 11 22 33 ? Only to match the same characters

---

## Comment 1

> Username: w4454962  
> Created at: 2019-06-22 17:49:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/518#issuecomment-504685587  

one = ch_p("1") || ch_p("2") || ch_p("3") ;  
two = repeat_p(2)[one];   
This solved the

---

## Comment 2

> Username: w4454962  
> Created at: 2019-06-22 17:51:32 UTC  
> Url: https://github.com/boostorg/spirit/issues/518#issuecomment-504685738  

but， How can I match 1 2 3 4 Incremental digital characters

---

## Comment 3

> Username: djowel  
> Created at: 2019-06-22 22:03:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/518#issuecomment-504701564  

This is not really an "issue" is it? Anyway, you can do it using syntactic predicates (e.g. [using eps](https://www.boost.org/doc/libs/1_54_0/libs/spirit/doc/html/spirit/qi/reference/auxiliary/eps.html)) or semantic actions that can "fail" the ongoing parse.
