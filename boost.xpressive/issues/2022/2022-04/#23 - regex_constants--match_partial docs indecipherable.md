# #23 - regex_constants::match_partial docs indecipherable [Open]

> Username: cppljevans  
> Created at: 2022-04-07 15:46:13 UTC  
> Updated at: 2022-04-07 15:46:13 UTC  
> Url: https://github.com/boostorg/xpressive/issues/23  

[the docs](https://www.boost.org/doc/libs/1_79_0_beta1/doc/html/boost/xpressive/regex_constants/match_flag_type.html)  
say:  
```  
match_partial  
  
    Specifies that if no match can be found, then it is acceptable to return a match [from, last) where from != last, if there exists some sequence of characters [from,to) of which [from,last) is a prefix, and which would result in a full match.   
```  
with no explanation of what `from`, `last` and `to` are.  One would guess that [from,last] is the string to be searched; however,  
if that's so, then there cannot be a [from,to] where [from,last] is a prefix, because that would require `last < to`, which can't  
be since the search string cannot extend past `last`.  
  
Please clarify.
