# #58 Add optional deps into generated config [Merged]

> Username: Neumann-A  
> Created at: 2024-04-19 21:34:40 UTC  
> Updated at: 2024-04-24 10:42:01 UTC  
> Merged at: 2024-04-24 10:23:54 UTC  
> Closed at: 2024-04-24 10:23:54 UTC  
> Url: https://github.com/boostorg/cmake/pull/58  

closes #52  
  
This allows to declare optional inter boost dependencies via using   
`$<TARGET_NAME_IF_EXISTS:Boost::(.*)>`  
instead of   
`Boost::(.*)`  
  
Target visibility can be controlled by the user via the cmake variables:  
`BOOST_INCLUDE_LIBRARIES/BOOST_EXCLUDE_LIBRARIES` on build.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2024-04-23 19:53:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/58#pullrequestreview-2018176774  

📁 include/BoostInstall.cmake

```diff
 316 |         string(APPEND CONFIG_FILE_CONTENTS "endif()\n")
 317 | 
 318 |+       elseif(dep MATCHES "^\\\$<TARGET_NAME_IF_EXISTS:Boost::(.*)>$")
```

> Username: pdimov  
> Created_at: 2024-04-23 19:53:18 UTC  
> Url: https://github.com/boostorg/cmake/pull/58#discussion_r1576817109  

Are the three slashes here correct? Shouldn't there be just two?

> Username: Neumann-A  
> Created_at: 2024-04-23 21:05:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/58#discussion_r1576891746  

Tested it. Both work. For some reason I made a habit out of using 3.

> Username: pdimov  
> Created_at: 2024-04-23 23:19:28 UTC  
> Url: https://github.com/boostorg/cmake/pull/58#discussion_r1577009192  

I think that the third slash just quotes the $ unnecessarily.  
  
Can you please remove it?


---

## Review 2 [Commented]

> Username: Neumann-A  
> Created_at: 2024-04-24 07:21:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/58#pullrequestreview-2019070077  

📁 include/BoostInstall.cmake

```diff
 316 |         string(APPEND CONFIG_FILE_CONTENTS "endif()\n")
 317 | 
 318 |+       elseif(dep MATCHES "^\\\$<TARGET_NAME_IF_EXISTS:Boost::(.*)>$")
```

> Username: Neumann-A  
> Created_at: 2024-04-24 07:21:44 UTC  
> Url: https://github.com/boostorg/cmake/pull/58#discussion_r1577405621  

```diff  
-      elseif(dep MATCHES "^\\\$<TARGET_NAME_IF_EXISTS:Boost::(.*)>$")  
+      elseif(dep MATCHES "^\\$<TARGET_NAME_IF_EXISTS:Boost::(.*)>$")  
```


---

## Comment 3

> Username: pdimov  
> Created_at: 2024-04-24 10:25:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/58#issuecomment-2074624802  

For background, this is needed by e.g. https://github.com/microsoft/vcpkg/pull/32309.

---
