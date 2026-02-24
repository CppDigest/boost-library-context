# #255 - boost directory iterator fails to construct under Windows with boost v1.79 and v1.80. [Closed]

> Username: vldevel  
> Created at: 2022-08-24 19:03:48 UTC  
> Updated at: 2022-12-02 13:23:54 UTC  
> Closed at: 2022-12-02 13:23:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/255  

Scratch that !  
  
With v1.80, it looks like it only fails under Wine. I.e. it is a Wine bug...

---

## Comment 1

> Username: Lastique  
> Created at: 2022-12-02 09:45:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/255#issuecomment-1334996775  

This seems to be [fixed](https://bugs.winehq.org/show_bug.cgi?id=53590) in Wine 7.21.
