# #1 Remove old Visual C++ 6 workaround [Merged]

> Username: danieljames  
> Created at: 2014-06-02 09:21:49 UTC  
> Updated at: 2014-06-05 10:23:47 UTC  
> Merged at: 2014-06-05 10:23:47 UTC  
> Closed at: 2014-06-05 10:23:47 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/1  

The changeset 41473987e50d123be1672fb7eaee45bbd60d8fca was never merged to release, and doesn't appear to be needed since Visual C++6 isn't supported any more. With this change, the only real difference between master and develop is Stephen Kelly's removal of other Visual C++ 6 workarounds (d4a8c7049a66663342c702d88802f518f403b5df).  
  
If you don't want to revert the old change, it could be cherry picked to master instead:  
  
```  
git cherry-pick 41473987e50d123be1672fb7eaee45bbd60d8fca  
```

---

## Comment 1

> Username: brandon-kohn  
> Created_at: 2014-06-02 11:02:44 UTC  
> Url: https://github.com/boostorg/numeric_conversion/pull/1#issuecomment-44824579  

If Fernando doesn't weigh in on this, I'll merge the request in a few days.

---
