# #151 - Leak child process if execv faild [Closed]

> Username: dvetutnev  
> Created at: 2020-03-22 19:15:45 UTC  
> Updated at: 2023-01-10 12:57:57 UTC  
> Closed at: 2023-01-10 12:57:57 UTC  
> Url: https://github.com/boostorg/process/issues/151  

Test: https://github.com/odant/conan-boost/blob/develop/test_package/test_boost_process_leak_child.cpp  
Patch: https://github.com/odant/conan-boost/blob/develop/fix_leak_child_process.patch

---

## Comment 1

> Username: pogaram  
> Created at: 2020-06-20 10:20:16 UTC  
> Updated at: 2020-06-20 10:28:25 UTC  
> Url: https://github.com/boostorg/process/issues/151#issuecomment-646973179  

It seems there is also a pipe leak in this case. I wrote about it in stackoverflow.  
https://stackoverflow.com/questions/62415639/boost-process-child-leaves-zombies-and-pipe-leaks

---

## Comment 2

> Username: arconamagi  
> Created at: 2022-02-12 16:09:28 UTC  
> Url: https://github.com/boostorg/process/issues/151#issuecomment-1037272987  

Are there any news on this issue? Was it fixed?

---

## Comment 3

> Username: martinus  
> Created at: 2023-01-10 12:51:41 UTC  
> Url: https://github.com/boostorg/process/issues/151#issuecomment-1377212940  

@klemens-morgenstern is this a duplicate of #265, can this now be closed?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-01-10 12:57:57 UTC  
> Url: https://github.com/boostorg/process/issues/151#issuecomment-1377219324  

I think so.
