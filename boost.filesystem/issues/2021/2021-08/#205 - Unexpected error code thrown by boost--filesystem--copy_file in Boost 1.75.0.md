# #205 - Unexpected error code thrown by boost::filesystem::copy_file in Boost 1.75.0 [Closed]

> Username: vaishnavkatiyar  
> Created at: 2021-08-30 04:56:02 UTC  
> Updated at: 2021-08-30 09:16:32 UTC  
> Closed at: 2021-08-30 09:16:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/205  

Following error codes are thrown by boost::filesystem::copy_file on Linux when the specified source path is a directory:  
  
Boost version 1.75.0 -> ENOSYS (function_not_supported)  
Boost version 1.72.0 -> EISDIR (is_a_directory)   
  
Code snippet:  
```  
#include <iostream>  
#include <boost/filesystem/operations.hpp>  
#include <boost/filesystem/path.hpp>  
  
int main() {  
    boost::filesystem::path p1("src_dir");  
    boost::filesystem::path p2("./dest_dir");  
    boost::system::error_code  ec;  
    copy_file(p1, p2, boost::filesystem::copy_option::none, ec);        
    if (ec)  
    {  
        std::cout << ec;  
    }  
    getchar();  
    return 0;  
}  
```  
- Command used for compilation  `g++ main.cpp -I./include ./lib/libboost_filesystem.a`  
- Repeat the same process for Boost 1.72.0  
  
Output with Boost 1.75.0 is system:38 -> ENOSYS (function_not_supported)  
Output with Boost 1.72.0 is system:21 -> EISDIR (is_a_directory)   
  
Issue:  
It looks to be a bug in Boost 1.75.0 as it is now throwing a different error code ENOSYS (function_not_supported) for boost::filesystem::copy_file, but at the same time, boost::filesystem::resize_file still throws the error code EISDIR (is_a_directory) when the specified source path is a directory.   
The error code EISDIR (is_a_directory) is the preferred choice since tells the user clearly that the specified source path is a directory.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-08-30 09:16:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/205#issuecomment-908182607  

`ENOSYS` is returned when a system call is not implemented by the kernel. This is usually a result of building Boost.Filesystem with kernel headers not matching the kernel that is running the binary, i.e. it's a build configuration issue. Boost.Filesystem 1.77 has runtime checks to mitigate this error.  
  
Duplicates https://github.com/boostorg/filesystem/issues/172.
