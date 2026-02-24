# #271 - std/boost remove_filename different output [Closed]

> Username: marakew  
> Created at: 2023-01-04 21:58:24 UTC  
> Updated at: 2023-01-04 22:13:28 UTC  
> Closed at: 2023-01-04 22:13:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/271  

[https://wandbox.org/permlink/iQDub9HJdmdSzqj6](https://wandbox.org/permlink/iQDub9HJdmdSzqj6)  
  
```  
C:/windows/disk/path/some/div/run.exe  
"C:/windows/disk/path/some/div/"  
"C:/windows/disk/path/some/div"  
std parent_path "C:/windows/disk/path/some/div"  
boost parent_path "C:/windows/disk/path/some"  
```  
  
is it boost correct  or std correct ?

---

## Comment 1

> Username: Lastique  
> Created at: 2023-01-04 22:13:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/271#issuecomment-1371491066  

They both are. Boost.Filesystem v3 is not equivalent to std::filesystem.
