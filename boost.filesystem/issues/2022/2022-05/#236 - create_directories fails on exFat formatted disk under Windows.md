# #236 - create_directories fails on exFat formatted disk under Windows [Closed]

> Username: truemanc  
> Created at: 2022-05-12 12:31:59 UTC  
> Updated at: 2022-05-15 10:48:42 UTC  
> Closed at: 2022-05-12 13:22:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/236  

I am using an exFat formatted disk under Windows.  
  
Start with this path:  
  
e:\tmp  
  
which is a folder.  
  
call create_directories("e:\\tmp\\boost")  
  
I'd expect the boost folder to be created in e:\\tmp  
  
In 1.79.0 it fails with an error saying e:\tmp already exists.  
  
It works as expected in 1.78.0  
  
I believe the root cause is the change to status(...). It calls detail::symlink_status(...) which calls GetFileInformationByHandleEx which (at least for the params used in filesystem) doesn't work on exFat formatted disks.  
  
I change my local copy to check if GetFileInformationByHandleEx fails and then call GetLastError to see if error code is INVALID_ERROR_PARAMETER. In this case I know the path element exists so I fall back and call GetFileInformationByHandle to retrieve the dwFileAttributes that tells us if the path element is a file or folder.  
  
There is already a branch in this function that calls GetFileInformationByHandle but it is only called if running on a version of Windows that doesn't support GetFileInformationByHandleEx.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-12 12:36:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/236#issuecomment-1124939706  

> In this case I know the path element exists so I fall back and call GetFileInformationByHandle to retrieve the dwFileAttributes that tells us if the path element is a file or folder.  
  
Do I understand correctly that [the other](https://github.com/boostorg/filesystem/blob/2896908264e7c70fb261b2851f25aed1d30fe903/src/operations.cpp#L3902-L3913) branch works in your case?

---

## Comment 2

> Username: truemanc  
> Created at: 2022-05-12 12:43:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/236#issuecomment-1124947303  

The other branch which calls GetFileInformationByHandle isn't used. It's only used if running on a version of Windows that doesn't support GetFileInformationByHandleEx.  
  
But yes, GetFileInformationByHandle works on the path element on exFat.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-05-12 12:46:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/236#issuecomment-1124950365  

Ok, thank you.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-05-12 13:23:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/236#issuecomment-1124989577  

I've committed a fix, please test if https://github.com/boostorg/filesystem/commit/15249ba87bf33aefe25f44a0510434d0fd1cb9d3 fixes the problem for you.

---

## Comment 5

> Username: truemanc  
> Created at: 2022-05-15 10:48:42 UTC  
> Url: https://github.com/boostorg/filesystem/issues/236#issuecomment-1126907149  

I have tried the latest code. create_directories() now works as expected on eFat formatted disks under Windows. Thanks.
