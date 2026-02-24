# #109 - No way to create empty + appendable files via file_descriptor c-tor [Open]

> Username: justinasvd  
> Created at: 2020-02-16 16:26:27 UTC  
> Updated at: 2020-02-16 16:34:17 UTC  
> Url: https://github.com/boostorg/iostreams/issues/109  

This is perhaps some corner case, but consider that you want to use `file_descriptor` to manage some log file. Writes to log files are typically write-only-to-the-end, but you may also want to do random reads on logs. Random reads via `pread` are no problem in Linux (they don't move file pointer), but random reads via `ReadFile` with offsets specified in `OVERLAPPED` struct actually move file pointer, so subsequent writes will not be at the end of file.  
  
The problem is fixed by opening a file with O_APPEND/FILE_APPEND_DATA when creating a new file. It would be nice to be able to forcefully open a "fresh" file with `std::ios_base::app flag`. I imagine this could be done by allowing `std::ios_base::trunc | std::ios_base::app` combo. The effect should be as follows:  
  
```C++  
#if defined(BOOST_IOSTREAMS_WINDOWS)  
    DWORD dwDesiredAccess;  
    if (enable_append) {  
        dwDesiredAccess = GENERIC_READ | FILE_APPEND_DATA | FILE_WRITE_ATTRIBUTES | FILE_WRITE_EA |  
                          STANDARD_RIGHTS_WRITE | SYNCHRONIZE;  
    } else {  
        dwDesiredAccess = GENERIC_READ | GENERIC_WRITE;  
    }  
  
    HANDLE file_handle =  
        ::CreateFileA(file_name.c_str(), dwDesiredAccess, FILE_SHARE_READ | FILE_SHARE_WRITE,  
                      NULL, // lpSecurityAttributes  
                      CREATE_ALWAYS, FILE_ATTRIBUTE_NORMAL,  
                      NULL); // hTemplateFile  
#else  
    // Calculate oflag argument to open.  
    int oflag = O_CREAT | O_TRUNC | O_RDWR;  
    if (enable_append) {  
        oflag |= O_APPEND;  
    }  
#ifdef _LARGEFILE64_SOURCE  
    oflag |= O_LARGEFILE;  
#endif  
  
    // Calculate pmode argument to open.  
    mode_t pmode = S_IRUSR | S_IWUSR | S_IRGRP | S_IWGRP | S_IROTH | S_IWOTH;  
  
    // Open file.  
    int file_handle = BOOST_IOSTREAMS_FD_OPEN(file_name.c_str(), oflag, pmode);  
#endif  
```
