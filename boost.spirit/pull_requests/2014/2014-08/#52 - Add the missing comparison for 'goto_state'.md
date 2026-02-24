# #52 Add the missing comparison for 'goto_state' [Merged]

> Username: maksqwe  
> Created at: 2014-08-23 15:42:53 UTC  
> Updated at: 2014-08-25 07:15:58 UTC  
> Merged at: 2014-08-25 00:07:21 UTC  
> Closed at: 2014-08-25 00:07:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/52  

``` C++  
return dfa == rhs_.dfa &&  
states == rhs_.states &&  
state == rhs_.state &&  
transitions == rhs_.transitions &&  
transition == rhs_.transition &&    // <===  
 end_state == rhs_.end_state &&  
id == rhs_.id &&  
unique_id == rhs_.unique_id &&  
goto_dfa == rhs_.goto_dfa &&  
bol_index == rhs_.bol_index &&  
eol_index == rhs_.eol_index &&  
token == rhs_.token &&  
transition == rhs_.transition;      // <===  
```

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-08-23 16:04:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/52#issuecomment-53157091  

Looks good to me. Would it be possible to add a minimal regression test case?

---
