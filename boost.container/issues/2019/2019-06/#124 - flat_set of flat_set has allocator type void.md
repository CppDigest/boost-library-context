# #124 - flat_set of flat_set has allocator type void [Closed]

> Username: thebrandre  
> Created at: 2019-06-25 16:54:52 UTC  
> Updated at: 2019-06-28 08:41:41 UTC  
> Closed at: 2019-06-28 08:41:41 UTC  
> Url: https://github.com/boostorg/container/issues/124  

Click [here](https://godbolt.org/z/WaUw6A) for a minimal example on compiler explorer.  
The snippet works with boost-1.69 but not with boost-1.70. That's the only difference between the two compiler windows (a click on 'output' reveals the error message).  
This seems to be related to issue #120 ... so it may already be fixed. Can anyone confirm?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-06-28 08:41:41 UTC  
> Url: https://github.com/boostorg/container/issues/124#issuecomment-506654779  

Yes, it's fixed.
