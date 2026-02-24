# #179 fix installation of vendored zlib and bzip2 [Merged]

> Username: grisumbras  
> Created at: 2024-11-06 15:01:05 UTC  
> Updated at: 2024-11-07 15:48:31 UTC  
> Merged at: 2024-11-07 15:48:30 UTC  
> Closed at: 2024-11-07 15:48:30 UTC  
> Url: https://github.com/boostorg/iostreams/pull/179  



---

## Comment 1

> Username: pdimov  
> Created_at: 2024-11-07 13:39:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/179#issuecomment-2462266648  

Seems to me that `install-zlib` is set but never used.

---

## Comment 2

> Username: grisumbras  
> Created_at: 2024-11-07 13:49:27 UTC  
> Url: https://github.com/boostorg/iostreams/pull/179#issuecomment-2462288216  

Sorry, forgot to remove that.

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2024-11-07 14:00:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/179#pullrequestreview-2421067225  

📁 build/Jamfile.v2

```diff
  40 | 
  43 |-     if [ os.environ ZLIB_SOURCE ]
  41 |+     if $(install_zlib)
```

> Username: pdimov  
> Created_at: 2024-11-07 14:00:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/179#discussion_r1832732200  

Shouldn't this be `! $(install-zlib)`?


---
