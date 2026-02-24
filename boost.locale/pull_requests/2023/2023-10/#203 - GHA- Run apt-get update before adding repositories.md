# #203 GHA: Run apt-get update before adding repositories [Merged]

> Username: Flamefire  
> Created at: 2023-10-15 17:19:10 UTC  
> Updated at: 2023-10-16 05:46:17 UTC  
> Merged at: 2023-10-16 05:09:38 UTC  
> Closed at: 2023-10-16 05:09:38 UTC  
> Url: https://github.com/boostorg/locale/pull/203  

Currently GHA fails on Ubuntu 18.04 with   
  
```  
Err:3 http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu bionic InRelease  
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 1E9377A2BA9EF27F  
  
W: GPG error: http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu bionic InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 1E9377A2BA9EF27F  
E: The repository 'http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu bionic InRelease' is not signed.  
```

---
