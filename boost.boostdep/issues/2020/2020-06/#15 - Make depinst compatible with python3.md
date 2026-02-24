# #15 - Make depinst compatible with python3 [Closed]

> Username: Flamefire  
> Created at: 2020-06-09 13:54:03 UTC  
> Updated at: 2020-06-09 14:13:53 UTC  
> Closed at: 2020-06-09 14:13:53 UTC  
> Url: https://github.com/boostorg/boostdep/issues/15  

Using Python3 (default on e.g. Windows GHA) depinst raises:  
  
```  
File "tools/boostdep/depinst/depinst.py", line 57  
    print 'Cannot determine module for header', h  
                                              ^  
    SyntaxError: Missing parentheses in call to 'print'. Did you mean print('Cannot determine module for header', h)?  
```  
  
I'd propose to make the tool at least Python3 compatible if not Python3 only given that Python2 is EOL already. However as current system `python` are often Python2 this might cause to much fallout.  
  
An easy fix would be to use `print('Cannot determine module for header ' + h)` style which is equivalent in Python2 or python 3 or use the `future` module which allows `print('Cannot determine module for header', h)` syntax in Python2 (actually it works already but prints `('Cannot determine module for header', 'aa')`)

---

## Comment 1

> Username: pdimov  
> Created at: 2020-06-09 14:10:35 UTC  
> Updated at: 2020-06-09 14:10:59 UTC  
> Url: https://github.com/boostorg/boostdep/issues/15#issuecomment-641323242  

See https://github.com/boostorg/boostdep/blob/9d5890819358d25d4482120650bc38e1199d4ebc/depinst/depinst.py#L12 and https://github.com/boostorg/boostdep/blob/9d5890819358d25d4482120650bc38e1199d4ebc/.travis.yml#L126-L139 although this code path is probably not exercised there, which is why it was missed.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-06-09 14:13:29 UTC  
> Url: https://github.com/boostorg/boostdep/issues/15#issuecomment-641324947  

Actually it looks like you're using an old depinst, that line reads differently.  
  
https://github.com/boostorg/boostdep/blob/9d5890819358d25d4482120650bc38e1199d4ebc/depinst/depinst.py#L68

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-06-09 14:13:53 UTC  
> Url: https://github.com/boostorg/boostdep/issues/15#issuecomment-641325175  

You are right, missed the update. Sorry for the noise
