# #83 - Does `_pass(ctx) = true` make sense? [Closed]

> Username: akrzemi1  
> Created at: 2024-01-27 14:28:03 UTC  
> Updated at: 2024-01-27 22:11:26 UTC  
> Closed at: 2024-01-27 22:11:26 UTC  
> Url: https://github.com/boostorg/parser/issues/83  

Form reading the docs, it looks like I may have a reason to call `_pass(ctx) = false`, because I may want to unconditionally fail the parsing, but there is no reason ever to call `_pass(ctx) = true`, because I still need to know if the syntax matches to my grammar. Am I correct?

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-27 22:11:26 UTC  
> Url: https://github.com/boostorg/parser/issues/83#issuecomment-1913350497  

Yes and no.  If you only call it once, that's correct.  The docs explicitly say so.  But if you ever set it to `false` you might want later to set it back to `true`.  For instance, you might do some loop like this:  
  
```c++  
_pass(ctx) = false;  
for (auto && x : whatevs) {  
    if (condition(x)) {  
        _pass(ctx) = true;  
        return;  
    }  
}  
```
