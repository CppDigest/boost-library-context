# #198 - posix_fadvise unavailable in Android prior to API 21 [Closed]

> Username: gjasny  
> Created at: 2021-07-12 11:37:19 UTC  
> Updated at: 2021-07-12 12:52:59 UTC  
> Closed at: 2021-07-12 12:52:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/198  

Hello,  
  
while testing Boost 1.77.0 Beta 1 Release Candidate 1 I noticed boost.filesystem fails to build on Android [here](https://github.com/boostorg/filesystem/blob/97f93a8cdc081b636d7491df5ef24bcd82138c51/src/operations.cpp#L677-L679):  
  
```  
clang-linux.compile.c++.without-pch /home/ec2-user/tmthq9fugik5ldb/_output/Applications_Android_armeabi-v7a_cxx17/boost/bin.v2/libs/filesystem/build/clang-linux-android/release/link-static/pch-off/target-os-android/threading-multi/visibility-global/operations.o  
libs/filesystem/src/operations.cpp:678:7: error: no member named 'posix_fadvise' in the global namespace  
    ::posix_fadvise(infile, 0, 0, POSIX_FADV_SEQUENTIAL);  
    ~~^  
1 error generated.  
```  
  
The reason is that we compile with Android API level 19 but the `posix_fadvise` function is only supported with at least 21:  
  
```C  
int posix_fadvise(int __fd, off_t __offset, off_t __length, int __advice) __RENAME_IF_FILE_OFFSET64(posix_fadvise64) __INTRODUCED_IN(21);  
```  
  
Could you please make the call conditional and bring the fix into Boost 1.77?  
  
Thanks,  
Gregor

---

## Comment 1

> Username: gjasny  
> Created at: 2021-07-12 11:45:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/198#issuecomment-878211099  

Maybe something like: https://github.com/boostorg/beast/blob/b7344b0d501f23f763a76488826dde8c31f34b5d/include/boost/beast/core/impl/file_posix.ipp#L26-L30

---

## Comment 2

> Username: Lastique  
> Created at: 2021-07-12 12:52:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/198#issuecomment-878254118  

Thanks for the report.
