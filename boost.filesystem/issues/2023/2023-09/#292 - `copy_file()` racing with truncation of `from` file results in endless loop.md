# #292 - `copy_file()` racing with truncation of `from` file results in endless loop [Closed]

> Username: jteh-cloudian  
> Created at: 2023-09-07 19:08:03 UTC  
> Updated at: 2023-09-07 20:00:51 UTC  
> Closed at: 2023-09-07 20:00:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/292  

With Boost 1.82.0, a program essentially doing:  
```cxx  
    boost::system::error_code ec;  
    boost::filesystem::copy_file(from, to, ec);  
```  
while a `truncate(2)` on the `from` file to reduce its size was performed in parallel ended up in an endless loop issuing this syscall repeatedly:  
```c  
copy_file_range(135, NULL, 140, NULL, 53687091, 0) = 0  
```  
The stack trace:  
```  
#0  0x00007f87dd24ea3d in syscall () from /lib/x86_64-linux-gnu/libc.so.6  
#1  0x000055c00b9bf0bc in boost::filesystem::detail::(anonymous namespace)::copy_file_data_copy_file_range::impl (  
    blksize=131072, size=268435456, outfile=140, infile=135) at libs/filesystem/src/operations.cpp:821  
#2  boost::filesystem::detail::(anonymous namespace)::check_fs_type<boost::filesystem::detail::(anonymous namespace)::copy_file_data_copy_file_range> (infile=135, outfile=140, size=268435456, blksize=131072) at libs/filesystem/src/operations.cpp:914  
#3  0x000055c00b9bfba6 in boost::filesystem::detail::copy_file (from=..., to=..., options=options@entry=0,  
    ec=ec@entry=0x7f87a77fc5b0) at libs/filesystem/src/operations.cpp:2923  
#4  0x000055c00b8fbf39 in boost::filesystem::copy_file (ec=..., to=..., from=...)  
    at /home/builder/.conan/data/boost/1.82.0/_/_/package/590520e63cc53e0ae1a2d4b6213fb209ae667a9b/include/boost/filesystem/operations.hpp:406  
```  
The (truncated) file was the same size at both `from` and `to`:  
```  
-rw------- 1 root root 214748365 Sep  6 15:15 20a1bc0e-4cc8-11ee-b3fa-2ef04cb1fe6f  
```  
and `53687091` is the difference between the `size` argument to `copy_file_data_copy_file_range::impl()` and the size of the file on-disk after truncation.  
It appears that `copy_file_data_copy_file_range::impl()` does not handle the case where `copy_file_range()` returns `0`, e.g. due to EOF.  
The same appears to be true for `copy_file_data_sendfile()`.  
Suggested fix:  
```diff  
diff --git a/src/operations.cpp b/src/operations.cpp  
index c7808d5..4e2209b 100644  
--- a/src/operations.cpp  
+++ b/src/operations.cpp  
@@ -863,6 +863,9 @@ struct copy_file_data_copy_file_range  
                 }  
  
                 return err;  
+            } else if (BOOST_UNLIKELY(sz == 0)) {  
+                // EOF  
+                break;  
             }  
  
             offset += sz;  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-09-07 20:00:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/292#issuecomment-1710699674  

Filesystem modification concurrent with Boost.Filesystem operations is a filesystem race condition, which is UB. The library does not provide any guarantees about its behavior in these conditions.  
  
That said, I may apply the change you suggest. But still, this won't make your use case supported or guaranteed.
