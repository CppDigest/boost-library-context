# #313 - boost::filesystem::file_size returns 0 for symbolic links on Windows [Closed]

> Username: TorstenHauska  
> Created at: 2024-06-18 08:57:12 UTC  
> Updated at: 2024-06-19 07:26:26 UTC  
> Closed at: 2024-06-18 19:34:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/313  

Hello,  
  
when executing boost::filesystem::file_size on a symbolic link on Windows the functions returns a size of 0. The same call on Linux returns the size of the file the symbolic link is pointing to. I'm unsure what the correct outcome should be but I would vote for the Linux version.  
  
I used boost version 1.85.0 on both systems.  
  
Here the code of a simple command line tool to reproduce the problem:  
```  
#include <iostream>  
  
#include <boost/filesystem.hpp>  
  
int main(int argc, char *argv[])  
{  
    if (argc < 2)  
        return -1;  
  
    std::uint64_t size = boost::filesystem::file_size(argv[1]);  
  
    std::cout << size << '\n';  
  
    return 0;  
}  
```  
Kind regards,  
Torsten Hauska

---

## Comment 1

> Username: Lastique  
> Created at: 2024-06-18 19:38:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/313#issuecomment-2176826547  

Thanks for the report.

---

## Comment 2

> Username: TorstenHauska  
> Created at: 2024-06-19 07:26:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/313#issuecomment-2177945319  

Thanks a lot for your fast fix. I am really impressed.
