# #169 Update WASI platform support [Closed]

> Username: whitequark  
> Created at: 2020-12-20 20:18:15 UTC  
> Updated at: 2020-12-23 22:46:41 UTC  
> Closed at: 2020-12-23 08:11:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/169  

This is essentially a part of #144 that I've missed because I tested against an outdated version of Boost at that time. See that PR for details.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-12-21 08:44:42 UTC  
> Url: https://github.com/boostorg/filesystem/pull/169#issuecomment-748849967  

If there is no access mode in WASI, then I'd prefer to also disable adding `S_IWUSR` above, along with its associated comment.

---

## Comment 2

> Username: whitequark  
> Created_at: 2020-12-22 00:39:01 UTC  
> Url: https://github.com/boostorg/filesystem/pull/169#issuecomment-749273553  

Something like this?

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-12-22 16:24:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/169#issuecomment-749633101  

More like this:  
  
```  
mode_t to_mode = from_mode;  
#if !defined(__wasm)  
...  
#endif  
```

---

## Comment 4

> Username: Lastique  
> Created_at: 2020-12-23 08:11:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/169#issuecomment-750000135  

Merged to develop.

---

## Comment 5

> Username: whitequark  
> Created_at: 2020-12-23 22:46:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/169#issuecomment-750510570  

Thanks!

---
