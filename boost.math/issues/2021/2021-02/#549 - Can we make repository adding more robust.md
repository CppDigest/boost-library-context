# #549 - Can we make repository adding more robust? [Closed]

> Username: jzmaddock  
> Created at: 2021-02-21 19:20:13 UTC  
> Updated at: 2021-02-25 17:09:33 UTC  
> Closed at: 2021-02-25 17:09:33 UTC  
> Url: https://github.com/boostorg/math/issues/549  

The answer to this may well be "no", but I'm seeing lots of CI failures due to:  
  
```  
gpg: keyring `/tmp/tmphatqqn4x/secring.gpg' created  
gpg: keyring `/tmp/tmphatqqn4x/pubring.gpg' created  
gpg: requesting key BA9EF27F from hkp server keyserver.ubuntu.com  
Error: retrieving gpg key timed out.  
gpg: keyserver timed out  
gpg: keyserver receive failed: keyserver error  
Error: Process completed with exit code 1.  
```  
  
This PR has 15 jobs failed with this: https://github.com/boostorg/math/pull/545 and it's a pain to check through them all.    
  
Is there any way we can auto-retry on failure a few times?

---

## Comment 1

> Username: mborland  
> Created at: 2021-02-25 17:00:22 UTC  
> Url: https://github.com/boostorg/math/issues/549#issuecomment-786052217  

This looks to be working as intended now  
  
![Retry Repo](https://user-images.githubusercontent.com/3745830/109188370-7e04c000-7758-11eb-8d70-856e5015faa3.png)

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-25 17:09:33 UTC  
> Url: https://github.com/boostorg/math/issues/549#issuecomment-786058640  

Good spot!  Closing down, thanks again for this.
