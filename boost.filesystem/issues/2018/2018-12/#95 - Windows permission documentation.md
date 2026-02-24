# #95 - Windows permission documentation [Closed]

> Username: redlars  
> Created at: 2018-12-19 08:00:04 UTC  
> Updated at: 2020-05-03 18:40:54 UTC  
> Closed at: 2020-05-03 18:40:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/95  

We found the description of filesystem perms enumerator [1] confussing and started a discussion on boost mailinglist [2]. It appears the filesystem uses the readonly attribute to set permissions and does not use Windows ACL at all. We believe that the current docs should be updated to give a better description of the permissions functionality.  
  
We also believe this windows warning[1] is somewhat hidden and it would be usefull if this warning was included in the permissons description [3].  
  
[1]  - https://www.boost.org/doc/libs/1_69_0/libs/filesystem/doc/reference.html#Enum-perms   
[2] - https://lists.boost.org/boost-users/2018/12/89232.php  
[3] - https://www.boost.org/doc/libs/1_69_0/libs/filesystem/doc/reference.html#permissions  
  
kind regards, Lars

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-03 18:40:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/95#issuecomment-623159900  

I believe the note in the docs is adequate. If you have a suggestion on how to improve it, please create a PR.
