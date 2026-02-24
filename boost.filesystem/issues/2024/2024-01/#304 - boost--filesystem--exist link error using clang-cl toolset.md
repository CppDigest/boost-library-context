# #304 - boost::filesystem::exist link error using clang-cl toolset. [Closed]

> Username: haydenzhourepo  
> Created at: 2024-01-16 04:10:36 UTC  
> Updated at: 2024-01-16 08:53:53 UTC  
> Closed at: 2024-01-16 08:53:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/304  

## env  
OS： Windows 11   
boost version： 1.83.0  
LLVM version: 17.0.1  
  
## How to reproduce  
```  
#include <iostream>  
#include <boost/filesystem.hpp>  
#include <filesystem>  
  
//namespace fs = std::filesystem;  
namespace fs = boost::filesystem;  
  
int main()  
{  
    fs::path path("abc");  
    if (fs::exists(path)) {  
        std::cout << "found!\n";  
    }  
    else {  
        std::cout << "not found!\n";  
    }  
}  
```  
compile the code above by the clang-cl toolset. will throw lld-link error.  
if I changed to use std::filesystem, then the codes compile succeed.  
  
## error  
![image](https://github.com/boostorg/filesystem/assets/21101605/9625b23c-c504-4bc7-aaa3-56eddead3e4d)  
![image](https://github.com/boostorg/filesystem/assets/21101605/66f6970a-fd5b-454a-baac-7a77eda15702)

---

## Comment 1

> Username: Lastique  
> Created at: 2024-01-16 08:53:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/304#issuecomment-1893306435  

Apparently, auto-linking doesn't work in your configuration. I think, some versions of clang-cl didn't support it, but I don't know which. Or you may be defining `BOOST_ALL_NO_LIB` or `BOOST_FILESYSTEM_NO_LIB` somewhere, which disables auto-linking.  
  
Without auto-linking, you need to explicitly specify Boost libraries in your linker command line, in your project settings.
