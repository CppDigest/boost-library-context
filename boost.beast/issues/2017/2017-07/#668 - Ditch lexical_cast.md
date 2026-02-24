# #668 - Ditch lexical_cast [Closed]

> Username: vinniefalco  
> Created at: 2017-07-21 01:50:15 UTC  
> Updated at: 2017-07-27 19:16:49 UTC  
> Closed at: 2017-07-27 19:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/668  

It causes these dependencies:  
```  
container (from lexical_cast)  
integer (from lexical_cast)  
math (from lexical_cast)  
mpl (from lexical_cast)  
numeric~conversion (from lexical_cast)  
```  
Those can be removed if lexical_cast is removed.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-27 19:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/668#issuecomment-318460011  

Merged in v88
