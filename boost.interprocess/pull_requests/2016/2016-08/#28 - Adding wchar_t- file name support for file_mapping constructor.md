# #28 Adding wchar_t* file name support for file_mapping constructor [Closed]

> Username: wingfiring  
> Created at: 2016-08-24 05:38:22 UTC  
> Updated at: 2021-02-03 10:16:04 UTC  
> Closed at: 2021-02-03 10:16:03 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28  

Hi,  
  
I'm a developer from Autodesk.    
  
More and more peoples in our company want to use file mapping cross platforms, but file_mapping of boost.interprocess  lacks of wide char file name supporting on Windows. I think it's valuable for us to support wide char file name.  
  
-Jackson

---

## Comment 1

> Username: egorpugin  
> Created_at: 2017-07-12 22:23:15 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28#issuecomment-314914819  

+1 for unicode windows file names.

---

## Comment 2

> Username: zachChilders  
> Created_at: 2019-06-24 17:11:08 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28#issuecomment-505098414  

Is there any specific reason this PR has not been merged in?  We're having some trouble with file paths that contain unicode as well and its becoming a pain to maintain multiple codepaths to handle native support on every build configuration we target.  Its one of the few areas where we cannot be truly xplat right now.

---

## Comment 3

> Username: mdoppler  
> Created_at: 2019-07-02 07:18:39 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28#issuecomment-507552501  

The same limitation applies to boost::interprocess::sync::file_lock.   
  
This patch adds support for wide character file paths in this class:  
  
[boost_1_70_0-windows-unicode-filelock.zip](https://github.com/boostorg/interprocess/files/3348773/boost_1_70_0-windows-unicode-filelock.zip)

---

## Comment 4

> Username: sepavel  
> Created_at: 2019-07-03 10:57:46 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28#issuecomment-508045560  

How can I vote for this patch?

---

## Comment 5

> Username: seanlis  
> Created_at: 2020-10-26 13:59:23 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28#issuecomment-716564397  

+1 for unicode windows file names.

---

## Comment 6

> Username: igaztanaga  
> Created_at: 2021-02-03 10:16:03 UTC  
> Url: https://github.com/boostorg/interprocess/pull/28#issuecomment-772395805  

Finally wide filename support for Windows was added in commit:  
https://github.com/boostorg/interprocess/commit/140b50efb3281fa3898f3a4cf939cfbda174718f

---
