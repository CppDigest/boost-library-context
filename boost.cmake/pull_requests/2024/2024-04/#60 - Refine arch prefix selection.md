# #60 Refine arch prefix selection [Merged]

> Username: Neumann-A  
> Created at: 2024-04-28 09:42:18 UTC  
> Updated at: 2024-10-13 00:48:39 UTC  
> Merged at: 2024-05-14 16:16:19 UTC  
> Closed at: 2024-05-14 16:16:20 UTC  
> Url: https://github.com/boostorg/cmake/pull/60  

as request in https://github.com/boostorg/cmake/pull/57#issuecomment-2081401809

---

## Review 1 [Commented]

> Username: Neumann-A  
> Created_at: 2024-04-28 09:43:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/60#pullrequestreview-2026962695  

📁 include/BoostInstall.cmake

```diff
 167 |+       else()
 168 |+         string(SUBSTRING "${CMAKE_SYSTEM_PROCESSOR}" 0 1 arch)
 169 |+         string(TOLOWER "${arch}" arch)
```

> Username: Neumann-A  
> Created_at: 2024-04-28 09:43:37 UTC  
> Url: https://github.com/boostorg/cmake/pull/60#discussion_r1582072473  

```diff  
-        string(TOLOWER "${arch}" arch)  
+      string(SUBSTRING "${CMAKE_SYSTEM_PROCESSOR}" 0 1 arch)  
+      string(TOLOWER "${arch}" arch)  
+      if(CMAKE_SYSTEM_PROCESSOR MATCHES "(i[3-6]86|amd64)")  
+        set(arch "x")  
+      endif()  
```  
Alternative?

> Username: Neumann-A  
> Created_at: 2024-04-28 09:48:45 UTC  
> Url: https://github.com/boostorg/cmake/pull/60#discussion_r1582074197  

Could probably also move         
```  
string(SUBSTRING "${CMAKE_SYSTEM_PROCESSOR}" 0 1 arch)  
string(TOLOWER "${arch}" arch)  
```  
out of the complete if block and remove the MSVC if block and merge the selection:  
```  
    string(SUBSTRING "${CMAKE_SYSTEM_PROCESSOR}" 0 1 arch)  
    string(TOLOWER "${arch}" arch)  
  
    if(CMAKE_CXX_COMPILER_ARCHITECTURE_ID MATCHES "^ARM")  
      set(arch "a")  
    elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "(i[3-6]86|[aA][mM][Dd]64)"  
        OR CMAKE_CXX_COMPILER_ARCHITECTURE_ID STREQUAL "X86"  
        OR CMAKE_CXX_COMPILER_ARCHITECTURE_ID STREQUAL "x64"  
      )  
      set(arch "x")  
    endif()  
```


---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-28 09:51:59 UTC  
> Url: https://github.com/boostorg/cmake/pull/60#issuecomment-2081415765  

No, CMAKE_SYSTEM_PROCESSOR is incorrect under MSVC, so it's best to keep the two branches separate.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-10-12 23:57:02 UTC  
> Url: https://github.com/boostorg/cmake/pull/60#issuecomment-2408749046  

This actually seems to derive `a64` for MinGW, so we don't have it quite right yet.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-10-13 00:48:38 UTC  
> Url: https://github.com/boostorg/cmake/pull/60#issuecomment-2408767660  

Ah, CMAKE_SYSTEM_PROCESSOR is "AMD64" there, uppercase. Will fix.

---
