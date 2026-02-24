# #370 gcc compiler warnings identified unhandled error cases [Merged]

> Username: jeking3  
> Created at: 2018-11-17 01:49:58 UTC  
> Updated at: 2021-10-02 21:19:28 UTC  
> Merged at: 2018-11-18 20:08:39 UTC  
> Closed at: 2018-11-18 20:08:39 UTC  
> Url: https://github.com/boostorg/build/pull/370  

error on fork had no output and did not end correctly

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2018-11-19 17:47:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/370#pullrequestreview-176419558  

📁 src/engine/debugger.c

```diff
1244 |         close( pipe1[ 1 ] );
1245 |         close( pipe2[ 0 ] );
1246 |         close( pipe1[ 1 ] );
```

> Username: jeking3  
> Created_at: 2018-11-19 17:47:21 UTC  
> Updated_at: 2018-11-19 17:51:45 UTC  
> Url: https://github.com/boostorg/build/pull/370#discussion_r234717956  

Didn't see this before, but there was a typo on this line that should be fixed.  It was already there, not part of this PR.  I opened another PR to fix it.


---
