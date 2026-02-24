# #133 - Add creation_time API in operations.hpp [Closed]

> Username: soroshsabz  
> Created at: 2020-04-11 21:52:18 UTC  
> Updated at: 2021-06-06 16:44:44 UTC  
> Closed at: 2021-06-06 16:44:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/133  

ITNOA  
  
I think it is good to add API to provide creation time of path. with below signature  
  
```  
std::time_t creation_time(const path& p)  
  
std::time_t creation_time(const path& p, system::error_code& ec) BOOST_NOEXCEPT  
```

---

## Comment 1

> Username: soroshsabz  
> Created at: 2020-04-18 18:28:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/133#issuecomment-615921595  

I try to ready a PR #134 to resolved this issue, and I think it is now ready for review. @Lastique If you have any comment or notes, I am very welcome for it :)

---

## Comment 2

> Username: Lastique  
> Created at: 2021-06-06 16:44:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/133#issuecomment-855427160  

This was implemented in Boost.Filesystem 1.75.
