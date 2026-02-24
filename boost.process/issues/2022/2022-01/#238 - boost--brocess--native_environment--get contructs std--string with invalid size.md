# #238 - boost::brocess::native_environment::get contructs std::string with invalid size [Closed]

> Username: ivan-efimov  
> Created at: 2022-01-31 20:35:58 UTC  
> Updated at: 2023-02-06 12:33:14 UTC  
> Closed at: 2023-02-06 12:33:14 UTC  
> Url: https://github.com/boostorg/process/issues/238  

Sample:  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/filesystem.hpp>  
#include <boost/process.hpp>  
  
void print_bytes(const std::string& s)  
{  
    for (auto ch : s)  
    {  
        std::cout << ch << "  ";  
    }  
    std::cout << "size() == " << s.size() << "\n";  
    std::cout << std::hex;  
    for (auto ch : s)  
    {  
        std::cout << std::setw(2) << (int)ch << " ";  
    }  
    std::cout << "\n\n";  
    std::cout << std::dec;  
}  
  
int main()  
{  
    boost::process::native_environment ne;  
  
    std::string with_getenv = std::getenv("WINDIR");  
    std::cout << "With getenv:\n";  
    print_bytes(with_getenv);  
  
    std::cout << "With boost::process::native_environment:\n";  
    std::string with_native_environment = ne.get("WINDIR");  
    print_bytes(with_native_environment);  
  
    // Real-life example  
    boost::filesystem::path windir = with_native_environment;  
    auto invalidPath = windir / "SomeNotExistingSubdir";  
  
    std::cout << std::boolalpha;  
  
    std::cout << "boost::process::native_environment-based path:\n";  
    print_bytes(windir.string());  
    // This path still works correct due to c-style internals  
    std::cout << windir << " exists: " << boost::filesystem::exists(windir) << "\n\n";  
  
    std::cout << "After joining:\n";  
    print_bytes(invalidPath.string());  
    // This path is treated as windir only due to \0  
    std::cout << invalidPath << " exists: " << boost::filesystem::exists(invalidPath) << "\n";  
    return 0;  
}  
```  
Output:  
```  
With getenv:  
C  :  \  W  i  n  d  o  w  s  size() == 10  
43 3a 5c 57 69 6e 64 6f 77 73  
  
With boost::process::native_environment:  
C  :  \  W  i  n  d  o  w  s     size() == 11  
43 3a 5c 57 69 6e 64 6f 77 73  0  
  
boost::process::native_environment-based path:  
C  :  \  W  i  n  d  o  w  s     size() == 11  
43 3a 5c 57 69 6e 64 6f 77 73  0  
  
"C:\Windows " exists: true  
  
After joining:  
C  :  \  W  i  n  d  o  w  s     \  S  o  m  e  N  o  t  E  x  i  s  t  i  n  g  S  u  b  d  i  r  size() == 33  
43 3a 5c 57 69 6e 64 6f 77 73  0 5c 53 6f 6d 65 4e 6f 74 45 78 69 73 74 69 6e 67 53 75 62 64 69 72  
  
"C:\Windows \SomeNotExistingSubdir" exists: true  
```  
Seems like `+1` [here](https://github.com/boostorg/process/blob/d231979a6c32a5cd1c96fbf5fc223d7875ee069e/include/boost/process/detail/windows/environment.hpp#L98) (and  [here](https://github.com/boostorg/process/blob/d231979a6c32a5cd1c96fbf5fc223d7875ee069e/include/boost/process/detail/windows/environment.hpp#L93)) makes it happen.  
  
P.S. Setup: Windows 10 21H1, boost 1.78.0
