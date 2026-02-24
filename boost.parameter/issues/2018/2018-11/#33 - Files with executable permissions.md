# #33 - Files with executable permissions [Closed]

> Username: eldiener  
> Created at: 2018-11-17 03:19:07 UTC  
> Updated at: 2018-11-17 17:56:25 UTC  
> Closed at: 2018-11-17 17:56:25 UTC  
> Url: https://github.com/boostorg/parameter/issues/33  

Most of the files that were added or changed recently had Linux executable permissions set on the source files. I updated 'develop' removing those executable permissions. No Boost git files should have the executable permission set for them unless, of course, they are really executable files, in which case they almost certainly don't belong under git.

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2018-11-17 16:42:29 UTC  
> Url: https://github.com/boostorg/parameter/issues/33#issuecomment-439630208  

Okay, I'll double-check the file permissions whenever I create a new source file or copy files over from a local repository.
