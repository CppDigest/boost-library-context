# #165 - boost filesystem create_directories [Closed]

> Username: Kawabaumga  
> Created at: 2020-11-10 11:24:29 UTC  
> Updated at: 2020-11-10 12:11:58 UTC  
> Closed at: 2020-11-10 12:11:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/165  

Hi,  
  
I'm using boost filesystem library to create some directories on a nfs directory.  
The function never timeout when there is a connexion problem with the nas.  
  
Did I miss something or is it the expected behaviour?  
  
Yours sincerely,  
Julien BAUMGARTEN

---

## Comment 1

> Username: Lastique  
> Created at: 2020-11-10 12:11:58 UTC  
> Url: https://github.com/boostorg/filesystem/issues/165#issuecomment-724662823  

I don't have experience with NFS, but there is no timeout argument in POSIX filesystem API, `mkdir` in particular. This means that the call may take as long as the underlying filesystem requires, including indefinitely.  
  
If there is a timeout, I would assume it should be set in the mount options or some filesystem-specific config file. How POSIX API behaves in the absence of such an option is filesystem-dependent and out of scope of Boost.Filesystem. If there is a timeout, I would assume upon its expiry `mkdir` should return an error code, which would in turn be reported by Boost.Filesystem to the caller.
