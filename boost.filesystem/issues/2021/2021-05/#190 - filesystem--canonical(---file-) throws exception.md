# #190 - filesystem::canonical("//file") throws exception [Closed]

> Username: TheKK  
> Created at: 2021-05-27 08:25:08 UTC  
> Updated at: 2021-06-06 16:29:24 UTC  
> Closed at: 2021-06-06 16:28:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/190  

**Description:**  
  
System: Linux,  
boost version: 1_0_69.  
  
Even when `/file` does exist, `filesystem::canonical("//file")` still failed.  
  
**Steps to reproduce:**  
  
1. create `/file`  
1. build & run `filesystem::canonical("//file")` in `/tmp/c`  
1. got exception  
  
```  
terminate called after throwing an instance of 'boost::filesystem::filesystem_error'  
  what():  boost::filesystem::canonical: No such file or directory: "//file/tmp/c"  
[1]    27916 abort (core dumped)  ./test-bin  
```  
  
**Notes:**  
  
I think this is bug since it works well with `filesystem::canonical("//tmp/file")` when `/tmp/file` exists.

---

## Comment 1

> Username: TheKK  
> Created at: 2021-05-27 08:27:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/190#issuecomment-849443271  

BTW, `path{"//file"}.is_absolute()` returns false.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-05-27 18:42:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/190#issuecomment-849856154  

Without looking at the code, the path starting with `//` may be interpreted as a UNC path and thus does not refer to a local filesystem. I'm not sure Boost.Filesystem fully supports UNC paths and not sure this is the reason for the exception you're getting, but I don't think your test is correct.

---

## Comment 3

> Username: TheKK  
> Created at: 2021-05-28 02:09:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/190#issuecomment-850059744  

I just know that path start with `//` could be interpreted by implementer freely so it's possible boost interprets it as UNC path. But if that's the case, it still makes no sense to me since I can observe these syscalls using `strace`:  
   
 ```  
getcwd("/tmp/c", 128)                   = 7  
stat("//file/tmp/c", 0x7ffdfec9db10)    = -1 ENOENT (No such file or directory)  
```   
  
AFAIK, `stat` only touch local device and do nothing special to path start with double slash, right?

---

## Comment 4

> Username: DMaroo  
> Created at: 2021-05-28 17:25:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/190#issuecomment-850563129  

If you trace the function calls, you get the following trace-back halfway through the execution:  
  
```  
canonical  
|  
|-- is_absolute  
    |  
    |-- has_root_directory  
        |  
        |-- root_directory  
            |  
            |-- root_directory_start  
```  
  
In the `root_directory_start` function, there is a dedicated `if` condition for cases in which the filenames start with `//`  
https://github.com/boostorg/filesystem/blob/267b94599348176951baf80aa19bd2cac63ca0ff/src/path.cpp#L551-L556  
  
As you can see, this essentially checks whether there's a `/` at the end of the path. If yes, then it returns `string_type::npos` (which if you retrace back a few function calls, corresponds to `is_absolute` returning `true`, and `canonical` working fine), else the function returns the value of `pos` (which corresponds to `is_absolute` returning `false`, and subsequently `canonical` failing and looking for `//file/tmp/c`, instead of `//file` ).  
  
Since the code has been explicitly designed to do so, I don't think this is a bug, from the code point of view. There must have been reasons why the developers chose to evaluate file-paths in such a manner. Changing the behavior might break existing code based on `boost::filesystem`, so, to avoid such errors, I think you should first parse such addresses yourself rather than giving them directly to boost. Changing boost's code-base does not seem like a good idea.  
  
Hope this helps.

---

## Comment 5

> Username: Lastique  
> Created at: 2021-06-06 16:28:03 UTC  
> Updated at: 2021-06-06 16:29:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/190#issuecomment-855424914  

The `//file` path is indeed interpreted as a UNC path with no root directory (the whole `//file` path is a root name). Since a root directory is required to be present in an absolute path, that path is correctly reported as relative. `canonical` must construct an absolute path, which it does by appending the current directory to the root name specified in the path, which yields `//file/tmp/c` in your case. It may look surprising at first, but the effect is the same as with any different root name, say `C:` -> `C:\tmp\c`. Since `//file/tmp/c` must be checked for symbolic links and must exist, `canonical` fails with an error.  
  
The support for UNC paths in Boost.Filesystem is partial. The `path` class recognizes UNC path prefix and reports path components and properties accordingly. This allows you to parse and compose UNC paths. But the actual use of the UNC paths with the OS is only supported if the OS supports it. In other words, if the OS does not support UNC paths then invoking Boost.Filesystem operations like `canonical` will also not work as intended. Of the operating systems that I know of, only Windows supports UNC paths.  
  
This level of support will not change any time soon. Boost.Filesystem will not implement the missing support for UNC paths for the OS. I also don't think that dropping support for UNC paths in the `path` class is sensible as that functionality can be useful even without the support from the OS.
