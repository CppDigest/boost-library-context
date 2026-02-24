# #174 - shared_memory_object constructor may fail on macOS [Closed]

> Username: kalikin  
> Created at: 2022-04-27 15:04:50 UTC  
> Updated at: 2022-07-16 22:28:29 UTC  
> Closed at: 2022-07-16 22:28:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/174  

Hi there. We've found that the subsequent `shared_memory_object` constructor calls may fail on macOS,  if the first call was made under a different user, e.g. *root*.  By first call I mean after system reboot:  
  
```c++  
 permissions p;  
 p.set_unrestricted();  
 shared_memory_object smo(open_or_create, "object1", read_write, p);  
```  
  
The error:  
> error: Dynamic exception type: boost::interprocess::interprocess_exception  
> std::exception::what: Operation not permitted  
  
I've created a [small demo](https://github.com/kalikin/shared_memory_object) for testing purposes.  
  
The problem is reproducible with Boost 1.78, 1.79 and also with Boost.Interprocess master branch. With Boost 1.77 everything works fine, as well as on Linux, Windows, FreeBSD. The error exists on all tested macOS versions: Catalina, Big Sur, Monterey, x64, aarch64.

---

## Comment 1

> Username: kalikin  
> Created at: 2022-05-02 15:53:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/174#issuecomment-1115060304  

We've discovered that in Boost 1.77, [create_directory](https://github.com/boostorg/interprocess/blob/7b2a37e614833f5fc7ab7d10df20aa11bc583bed/include/boost/interprocess/detail/os_file_functions.hpp#L495-L496) function failed immediately on `mkdir()`, setting *already exist* error, without calling `chmod()`:  
  
```c++  
inline bool create_directory(const char *path) {    
  return ::mkdir(path, 0777) == 0 && ::chmod(path, 0777) == 0;   
}  
```  
  
The error code then was checked in [create_shared_dir_and_clean_old](https://github.com/boostorg/interprocess/blob/7b2a37e614833f5fc7ab7d10df20aa11bc583bed/include/boost/interprocess/detail/shared_dir_helpers.hpp#L198-L203) function, which successfully ignored it:  
  
```c++  
if (!create_directory(root_shared_dir.c_str())) {  
  error_info info(system_error_code());  
  if (info.get_error_code() != already_exists_error) {  
    throw interprocess_exception(info);  
  }  
}  
```  
---  
In version 1.78 the code has changed. [open_or_create_shared_directory](https://github.com/boostorg/interprocess/blob/c4a046793e07ac171ffba395402f8c9a6011cfc2/include/boost/interprocess/detail/os_file_functions.hpp#L522-L527) function now always calls `chmod()` if the directory already exists:  
```c++  
inline bool open_or_create_shared_directory(const char *path) {  
  ::mode_t m = ::mode_t(01777);  
  const bool created_or_exists = (::mkdir(path, m) == 0) || (errno == EEXIST);  
  return created_or_exists && (::chmod(path, m) == 0);  
}  
```  
The function fails with `chmod()`, setting permission error, and in [create_shared_dir_and_clean_old](https://github.com/boostorg/interprocess/blob/c4a046793e07ac171ffba395402f8c9a6011cfc2/include/boost/interprocess/detail/shared_dir_helpers.hpp#L212-L215) exception is thrown:  
```c++  
if (!open_or_create_shared_directory(root_shared_dir.c_str())) {  
  error_info info(system_error_code());  
  throw interprocess_exception(info);  
}  
```  
___  
If the directory already existed `chmod()` shouldn't be called or its result should be ignored, because it fails if the directory was created by another user.

---

## Comment 2

> Username: vladimirkondratyev  
> Created at: 2022-05-18 10:53:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/174#issuecomment-1129861944  

Just want to clarify that shared_memory_object is completely broken under macOS in Boost 1.78+. open_or_create_shared_directory works only for the same user who created SMO.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2022-07-16 22:19:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/174#issuecomment-1186308759  

That logic was modified to make sure (maybe wrongly) that the sticky bit (see issue #157, "Set sticky bit on shared interprocess folder") is set also in old (unsafe) installations.  
  
But you are right, the chmod error should be ignored in case the directory already exists.
