# #23 - C++17 fixes not merged to master [Closed]

> Username: pdimov  
> Created at: 2017-11-20 19:39:50 UTC  
> Updated at: 2018-01-20 19:37:48 UTC  
> Closed at: 2018-01-20 19:37:47 UTC  
> Url: https://github.com/boostorg/locale/issues/23  

The commit  
  
https://github.com/boostorg/locale/commit/322437a485c63d9fce4dc620597b6c75b6396daf  
  
that enables compilation on C++17 has not been applied to master. It's too late for the 1.66 beta, but perhaps it needs to be applied for the 1.66 release once master is open for changes?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-11-21 07:02:49 UTC  
> Url: https://github.com/boostorg/locale/issues/23#issuecomment-345935460  

Ok good point

---

## Comment 2

> Username: pdimov  
> Created at: 2018-01-15 19:15:46 UTC  
> Url: https://github.com/boostorg/locale/issues/23#issuecomment-357767358  

`master` still doesn't build on C++17.

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2018-01-20 19:37:47 UTC  
> Url: https://github.com/boostorg/locale/issues/23#issuecomment-359196612  

Merged all changes in develop to master.  
  
Now should be fixed
