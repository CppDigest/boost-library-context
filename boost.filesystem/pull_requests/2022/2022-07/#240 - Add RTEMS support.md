# #240 Add RTEMS support [Closed]

> Username: kiwichris  
> Created at: 2022-07-07 02:02:29 UTC  
> Updated at: 2022-07-07 20:43:52 UTC  
> Closed at: 2022-07-07 20:43:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/240  

This change lets the boost filesystem support build on RTEMS.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2022-07-07 08:36:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/240#pullrequestreview-1031199560  

📁 src/operations.cpp

```diff
3389 |     !(defined(__SUNPRO_CC) || defined(__sun) || defined(sun)) && \
3390 |-     !(defined(linux) || defined(__linux) || defined(__linux__)) && \
3390 |+     !(defined(linux) || defined(__linux) || defined(__linux__) || defined(__rtems__)) && \
```

> Username: Lastique  
> Created_at: 2022-07-07 08:36:40 UTC  
> Url: https://github.com/boostorg/filesystem/pull/240#discussion_r915612164  

Please, separate RTEMS check into its own separate line. One OS per line, please.


---

## Comment 2

> Username: Lastique  
> Created_at: 2022-07-07 20:43:52 UTC  
> Url: https://github.com/boostorg/filesystem/pull/240#issuecomment-1178205124  

I have made the change myself. Thanks.

---
