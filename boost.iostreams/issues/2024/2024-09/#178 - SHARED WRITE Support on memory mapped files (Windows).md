# #178 - SHARED WRITE Support on memory mapped files (Windows) [Open]

> Username: piesarmin  
> Created at: 2024-09-20 14:36:08 UTC  
> Updated at: 2024-09-20 16:35:32 UTC  
> Url: https://github.com/boostorg/iostreams/issues/178  

We have been using the Boost libraries for at least 14 years. Unfortunately, since that time, we have had to patch a file with every new release in order to use the iostreams library on Windows.  
That never had been a problem under Unix or Linux.  
  
The file in question is from boost::iostreams. It is <boost>\libs\iostreams\src\mapped_file.cpp.  
  
The actual code only allows shared read access to the opened file by calling  
```  
      ::CreateFileA(   
            p.path.c_str(),  
            dwDesiredAccess,  
            FILE_SHARE_READ,  
            NULL,  
            dwCreationDisposition,  
            dwFlagsandAttributes,  
            NULL );  
```  
  
We have to change **FILE_SHARE_READ** to **FILE_SHARE_READ | FILE_SHARE_WRITE**:  
  
```  
      ::CreateFileA(   
            p.path.c_str(),  
            dwDesiredAccess,  
            FILE_SHARE_READ | FILE_SHARE_WRITE,  
            NULL,  
            dwCreationDisposition,  
            dwFlagsandAttributes,  
            NULL );  
```  
  
Background: We open a part of a file read-only in our application in multiple threads or processes per memory mapping while we allow one other thread or process to append data at the actual end of the file.  
  
We opened an issue at that time (link to the old ticket system: https://svn.boost.org/trac/boost/ticket/5595 ), but somehow never received any feedback. Eventually, we gave up.  
  
We hope that someone will address this issue now (maybe there’s also another workaround).  
  
Best regards,  
Armin
