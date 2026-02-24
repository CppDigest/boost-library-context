# #98 - Windows: boost::interprocess::file_lock blocks all I/O when locked [Closed]

> Username: grmcdorman  
> Created at: 2019-10-09 19:53:18 UTC  
> Updated at: 2021-08-23 12:01:00 UTC  
> Closed at: 2021-08-23 12:01:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/98  

Platform: Win32, 64-bit (Windows 10)  
Compiler: Visual C++ 2015 (14.0.25431.01 Update 3)  
Boost Version: 1.70  
  
The following test code:  
  
    boost::interprocess::file_lock flock(path);  
    {  
        boost::interprocess::scoped_lock<boost::interprocess::file_lock> held_lock(flock);  
        {  
            std::ofstream output(path);  
            output << "Hello" << std::endl;  
            output.flush();  
            if (output.bad()) {  
                std::cout << "Failed to write to output" << std::endl;  
            }  
        }  
        std::cout << "Stream complete" << std::endl;  
    }  
    std::cout << "File unlocked" << std::endl;  
  
fails to write anything to `path`, and reports that `output.bad()` is true.  
  
From inspecting the source, on Windows `file_lock` uses `LockFileEx`. According to the MSDN documentation, `LockFileEx` prevents _any_ other handle from accessing the file, even in the same process; specifically:  
  
> If the locking process opens the file a second time, it cannot access the specified region through this second handle until it unlocks the region.  
  
This is actually a bit misleading; opening the file _before_ acquiring the lock also has the same issue.  
  
Testing with similar code using the Win32 API directly results in the same issue; all subsequent I/O must use the same handle used to lock the file.  
  
Note that while using a dummy file name would avoid the I/O issue, this has two side effects:  
* It is not functionally different from a named mutex in this case (although, unlike named mutexes, it is automatically released if the application terminates abnormally, e.g. a crash);  
* The dummy file must be pre-created and must not duplicate any existing, expected file.  
  
As such, either boost must use a locking mechanism that does not use `LockFileEx` (perhaps `named_mutex`?), or some (generic) way of obtaining a stream from the lock is required. Unfortunately the latter seems to be a breaking change in `file_lock` behaviour.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-23 12:01:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/98#issuecomment-903700095  

Reviewing old bugs.  
  
Thanks for the report. Native file locking in Windows is mandatory and Boost.Interprocess documentation was misleading. Using mandatory locking for portability layers in windows is usual (Apache Portable, PHP, etc...) as implementing advisory locks in Windows is very difficult (it would require a cygwin-like complex emulation).
