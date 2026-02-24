# #84 - Named mutex might not value permissions correctly [Open]

> Username: DasRoteSkelett  
> Created at: 2019-04-12 14:41:14 UTC  
> Updated at: 2024-02-27 11:14:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/84  

Hi!  
  
When using a named_mutex within POSIX, and setting the Permissions Object, everything boils down to an ::sem_open call. There, the permissions are passed. But ::sem_open masks the permissions with the processes umask. Therefore, the semaphore might not have 0666 permissions, but e.g. 644, even though, permissions were passed with set_unrestricted().  
  
Workaround there would be setting umask to 0 before ::sem_open, and restoring again after.   
At least, this behaviour should be documented.   
  
Regards,  
  
  Matthias

---

## Comment 1

> Username: romainreignier  
> Created at: 2024-02-27 11:14:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/84#issuecomment-1966320962  

Indeed, the chmod is commented [here](https://github.com/boostorg/interprocess/blob/8e4caa9fca8ff92b739b570f399f4407da0ecc2f/include/boost/interprocess/sync/posix/semaphore_wrapper.hpp#L84) without any documentation or workaround described.
