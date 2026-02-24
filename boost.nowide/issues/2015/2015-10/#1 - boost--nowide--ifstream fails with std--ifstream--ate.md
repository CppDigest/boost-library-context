# #1 - boost::nowide::ifstream fails with std::ifstream::ate [Closed]

> Username: Zireael-N  
> Created at: 2015-10-05 14:35:35 UTC  
> Updated at: 2015-12-07 18:45:07 UTC  
> Closed at: 2015-12-07 18:45:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/1  

This piece of code throws an exception:  
  
``` cpp  
std::ifstream::pos_type filesize(const char* filename)  
{  
    boost::nowide::ifstream in(filename, std::ifstream::ate | std::ifstream::binary);  
    if (!in)  
        throw std::runtime_error("Couldn't open the file.");  
    return in.tellg();  
}  
```  
  
_If I get rid of std::ifstream::ate, it doesn't throw an exception._  
  
This piece of code works fine:  
  
``` cpp  
std::ifstream::pos_type filesize(const char* filename)  
{  
    std::ifstream in(boost::nowide::widen(filename).c_str(), std::ifstream::ate | std::ifstream::binary);  
    if (!in)  
        throw std::runtime_error("Couldn't open the file.");  
    return in.tellg();  
}  
```  
  
Compiling with MSVC.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2015-10-05 19:55:14 UTC  
> Url: https://github.com/boostorg/nowide/issues/1#issuecomment-145649646  

I just forgot to implement ate...  
 Artyom Beilis -------------- CppCMS - C++ Web Framework:   http://cppcms.com/ CppDB - C++ SQL Connectivity: http://cppcms.com/sql/cppdb/  
  
```  
  From: Zireael <notifications@github.com>  
```  
  
 To: artyom-beilis/nowide nowide@noreply.github.com   
 Sent: Monday, October 5, 2015 5:35 PM  
 Subject: [nowide] boost::nowide::ifstream fails with std::ifstream::ate (#1)  
  
This piece of code throws an exception:std::ifstream::pos_type filesize(const char\* filename)  
{  
    boost::nowide::ifstream in(filename, std::ifstream::ate | std::ifstream::binary);  
    if (!in)  
        throw std::runtime_error("Couldn't open the file.");  
    return in.tellg();  
}If I get rid of std::ifstream::ate, it doesn't throw an exception.This piece of code works fine:std::ifstream::pos_type filesize(const char\* filename)  
{  
    std::ifstream in(boost::nowide::widen(filename).c_str(), std::ifstream::ate | std::ifstream::binary);  
    if (!in)  
        throw std::runtime_error("Couldn't open the file.");  
    return in.tellg();  
}—  
Reply to this email directly or view it on GitHub.

---

## Comment 2

> Username: MikaelSmith  
> Created at: 2015-12-07 18:41:20 UTC  
> Url: https://github.com/boostorg/nowide/issues/1#issuecomment-162618659  

Did https://github.com/artyom-beilis/nowide/commit/eaff48de2950ffb6de92ed81c277e5b748e719b7 fix this issue? If so it'd be nice to close it.

---

## Comment 3

> Username: artyom-beilis  
> Created at: 2015-12-07 18:45:07 UTC  
> Url: https://github.com/boostorg/nowide/issues/1#issuecomment-162620348  

Yep it is
