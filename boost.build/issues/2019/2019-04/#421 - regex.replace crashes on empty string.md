# #421 - regex.replace crashes on empty string [Closed]

> Username: Kojoley  
> Created at: 2019-04-03 00:37:56 UTC  
> Updated at: 2019-04-03 17:30:24 UTC  
> Closed at: 2019-04-03 17:30:24 UTC  
> Url: https://github.com/boostorg/build/issues/421  

```jam  
import regex ;  
  
regex.replace "" "" "foo" ;  
```
