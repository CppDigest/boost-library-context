# #261 - Memory Mapping Empty Files Fails [Closed]

> Username: rroessler  
> Created at: 2025-04-14 16:23:20 UTC  
> Updated at: 2025-11-13 22:33:50 UTC  
> Closed at: 2025-11-13 22:33:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/261  

If a file is mapped that is empty (eg: size of 0), then `mmap` will fail on POSIX with an `invalid_argument` error code. There should be an edge case for this as files can validly have no data.

---

## Comment 1

> Username: rroessler  
> Created at: 2025-04-14 16:24:19 UTC  
> Updated at: 2025-04-14 16:24:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/261#issuecomment-2802244718  

This specifically occurs when running the following with an empty file:  
  
```c++  
auto mapping = boost::interprocess::file_mapping("...", boost::interprocess::read_only);  
auto region = boost::interprocess::mapped_region(mapping, boost::interprocess::read_only);  
```

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-04-14 19:16:56 UTC  
> Url: https://github.com/boostorg/interprocess/issues/261#issuecomment-2802755967  

This behavior is consistent on Windows and Linux, it's not an oversight, we can explicitly state it in the documentation, however.

---

## Comment 3

> Username: rroessler  
> Created at: 2025-05-09 00:38:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/261#issuecomment-2864778700  

Although this behaviour is consistent with how `mmap` works, this does not work intuitively with empty files which do allow for having a size of zero.  
  
Agreeable there should be something within the documentation about this, as well as for how to overcome this issue. Something that could suffice would be:  
  
```cpp  
auto filepath = ".../path/to/file";  
boost::interprocess::offset_t size = 0;  
auto readonly = boost::interprocess::read_only;  
  
auto mapping = boost::interprocess::file_mapping(filepath, readonly);  
boost::interprocess::ipcdetail::get_file_size(mapping.get_mapping_handle().handle, size);  
auto mapping = boost::interprocess::mapped_region();  
if (size) mapping = boost::interprocess::mapped_region(mapping, readonly);  
```  
  
This also highlights how getting around the `mmap` restriction, specifically for file mappings requires a second file-size lookup.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2025-11-13 22:18:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/261#issuecomment-3529947825  

Since using a `ipcdetail` function is not acceptable for users, I'll add `file_mapping::get_size()` and document that the mapping and "size" can't be empty.
