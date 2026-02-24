# #132 mpi::environment: add library_version() method (#129) [Merged]

> Username: leha-bot  
> Created at: 2021-08-10 14:58:55 UTC  
> Updated at: 2022-06-02 12:43:55 UTC  
> Merged at: 2022-06-02 12:43:54 UTC  
> Closed at: 2022-06-02 12:43:54 UTC  
> Url: https://github.com/boostorg/mpi/pull/132  

Based on MPI-3 `MPI_Get_library_version()` API.  
On previous versions it returns an empty string.  
  
Fixes #129.

---

## Comment 1

> Username: leha-bot  
> Created_at: 2022-05-21 18:08:09 UTC  
> Url: https://github.com/boostorg/mpi/pull/132#issuecomment-1133745180  

@aminiussi friendly ping 😊

---

## Comment 2

> Username: aminiussi  
> Created_at: 2022-05-23 10:06:57 UTC  
> Url: https://github.com/boostorg/mpi/pull/132#issuecomment-1134472955  

Sorry, I'm blocking a day next week to check it.

---

## Review 3 [Commented]

> Username: aminiussi  
> Created_at: 2022-06-02 12:38:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mpi/pull/132#pullrequestreview-993471625  

📁 src/environment.cpp

```diff
 262 |+   int len = 0;
 263 |+   BOOST_MPI_CHECK_RESULT(MPI_Get_library_version, (lib_version, &len));
 264 |+   return std::string(result, len);
```

> Username: aminiussi  
> Created_at: 2022-06-02 12:38:39 UTC  
> Url: https://github.com/boostorg/mpi/pull/132#discussion_r887903797  

this should be `return std::string(lib_version, len);` right ?


---
