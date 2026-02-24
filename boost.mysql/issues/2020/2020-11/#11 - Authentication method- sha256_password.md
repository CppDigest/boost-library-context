# #11 - Authentication method: sha256_password [Closed]

> Username: anarthal  
> Created at: 2020-11-29 17:24:57 UTC  
> Updated at: 2024-01-02 20:57:48 UTC  
> Closed at: 2024-01-02 20:57:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/11  

Add support for the authentication method sha256_password.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-01-02 20:57:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/11#issuecomment-1874548752  

This authentication method is deprecated. `caching_sha2_password` (which we already support) is widely available, and should be used instead.
