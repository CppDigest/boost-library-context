# #7 - Example in the documentation doesn't work (in 1.70.0 [Closed]

> Username: brucestephens  
> Created at: 2019-07-01 11:46:48 UTC  
> Updated at: 2019-11-04 16:58:39 UTC  
> Closed at: 2019-11-01 18:54:06 UTC  
> Url: https://github.com/boostorg/bcp/issues/7  

I tried  
  
    bcp regex config build /tmp/dest  
  
(I don't care about the namespace things and I'm aware they may not work anyway.)  
  
Trying to build in `/tmp/dest` caused an error with `boost-install` missing, so I added `boost_install`. Then `log` and `predef`. Then there's an error I can't seem to fix:  
```  
error: Unable to find file or target named  
error:     '/boost/headers'  
error: referred to from project at  
error:     'libs/atomic/build'  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-01 18:54:06 UTC  
> Url: https://github.com/boostorg/bcp/issues/7#issuecomment-548909088  

This should now be fixed in develop.  
  
A temporary workaround would be to add "boost_install headers" to the list of modules to copy - though this pulls in too much on the whole.

---

## Comment 2

> Username: brucestephens  
> Created at: 2019-11-04 16:58:39 UTC  
> Url: https://github.com/boostorg/bcp/issues/7#issuecomment-549448421  

Thanks very much. I can confirm that it's resolved in develop. I couldn't get the workaround to work but just copying the changed `add_path.cpp` from develop works fine and is sufficient for what I want.
