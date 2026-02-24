# #76 - lexically_relative and lexically_poximate give unexpected result [Closed]

> Username: madebr  
> Created at: 2018-06-21 16:49:35 UTC  
> Updated at: 2019-10-05 16:30:05 UTC  
> Closed at: 2019-10-05 16:17:52 UTC  
> Url: https://github.com/boostorg/filesystem/issues/76  

`/home/user/documents/file.txt` relative to `/home/user/.` should be `documents/file.txt`.  
  
c++17 gives the expected result.  
  
```  
#include <filesystem>  
#include <iostream>  
  
namespace fs = std::filesystem;  
  
#include <boost/filesystem.hpp>  
  
namespace bfs = boost::filesystem;  
  
int main() {  
    std::string hs = "/home/user/.";  
    std::string ps = "/home/user/documents/file.txt";  
  
    {  
        fs::path h = hs;  
        fs::path p = ps;  
        std::cout << "relative: " << p.lexically_relative(h) << '\n'  
                  << "proximate: " << p.lexically_proximate(h) << '\n';  
    }  
  
    {  
        bfs::path h = hs;  
        bfs::path p = ps;  
        std::cout << "relative: " << p.lexically_relative(h) << '\n'  
                  << "proximate: " << p.lexically_relative(h) << '\n';  
    }  
  
    return 0;  
}  
```  
outputs  
```  
relative: "documents/file.txt"  
proximate: "documents/file.txt"  
relative: "../documents/file.txt"  
proximate: "../documents/file.txt"  
```

---

## Comment 1

> Username: RichardCrewe  
> Created at: 2019-08-14 11:41:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/76#issuecomment-521211527  

I've been using c++14 and have encountered this problem on OSX, but not Linux.  
  
Boost 1.67  
Compiler on OSX: Apple LLVM version 10.0.0 (clang-1000.10.44.4)  
Compiler on Linux: g++ (Ubuntu 5.4.0-6ubuntu1~16.04.11) 5.4.0 20160609

---

## Comment 2

> Username: RichardCrewe  
> Created at: 2019-08-14 13:55:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/76#issuecomment-521256063  

The trailing dot in the parent path (`hs` in the example above) is confusing `lexically_relative`. In my case I discovered it as the `boost::filesystem::temp_directory_path()` returns a path with a trailing slash on OSX, but not Linux. This trailing slash is effectively normalised to a trailing slash dot by the path iterator.

---

## Comment 3

> Username: rcdailey  
> Created at: 2019-10-04 14:41:38 UTC  
> Updated at: 2019-10-04 14:43:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/76#issuecomment-538426121  

I'm seeing this issue in Boost 1.70. Here is my code example:  
  
```cpp  
namespace fs = boost::filesystem;  
  
int main()  
{  
    fs::path parentPath("/home/user1/");  
    fs::path childPath("/home/user1/Downloads/Books");  
    std::cout << childPath.lexically_relative(parentPath) << '\n';  
}  
```  
  
[Live sample is here](https://wandbox.org/permlink/npcAiNJ7DKyJyssQ)  
  
The output is `"../Downloads/Books"` but should actually be `"Downloads/Books"`. When will this get fixed?  
  
EDIT: Looks like `boost::filesystem::relative()` also has this issue:  
  
```cpp  
std::cout << fs::relative(childPath, parentPath) << '\n';  
```  
  
Outputs: `"../Downloads/Books"`.

---

## Comment 4

> Username: rcdailey  
> Created at: 2019-10-05 16:30:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/76#issuecomment-538665241  

@Lastique Thank you for the fix!!
