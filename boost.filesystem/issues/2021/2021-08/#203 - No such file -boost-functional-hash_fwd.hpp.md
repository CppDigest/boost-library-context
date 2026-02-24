# #203 - No such file <boost/functional/hash_fwd.hpp> [Closed]

> Username: IamtheMZI  
> Created at: 2021-08-11 20:40:00 UTC  
> Updated at: 2021-08-12 08:20:43 UTC  
> Closed at: 2021-08-12 08:20:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/203  

https://github.com/boostorg/filesystem/blob/9794725bda2e612f1fdcadd98cdce5b7bfb548f1/include/boost/filesystem/path.hpp#L26  
  
Hi,   
I am trying to build boost filesystem with tag boost-1.76.0 and functional seems like a dependency for this. However I am getting an error like this   
```  
/path.hpp:33:10: fatal error: boost/functional/hash_fwd.hpp: No such file or directory  
   33 | #include <boost/functional/hash_fwd.hpp>  
```  
  
I tried to find this file but doesn't look like any such file exists.   
Can someone look into it or clarify this to me?   
Thanks.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-08-12 08:20:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/203#issuecomment-897443205  

Duplicates https://github.com/boostorg/log/issues/158. The file is part of [Boost.ContainerHash](https://github.com/boostorg/container_hash/blob/fd2781afc98ab2ec61b647f80d9a3c3a0864a82e/include/boost/functional/hash_fwd.hpp).
