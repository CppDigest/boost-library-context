# #201 - `weakly_canonical()` throws `The system cannot find the file specified` for some paths containing `..` on Windows [Closed]

> Username: cameel  
> Created at: 2021-07-28 04:14:31 UTC  
> Updated at: 2021-07-28 17:23:24 UTC  
> Closed at: 2021-07-28 17:00:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/201  

I found a few examples of paths that make `weakly_canonical()` throw an exception with the following message on Windows:  
```  
boost::filesystem::weakly_canonical: The system cannot find the file specified  
```  
  
Here are the paths that trigger this (they do not actually exist in the filesystem):  
1. `/a/../a/b/`  
2. `a/../a/b/`  
3. `/../a/..`  
4. `/../../a/../`  
  
I'd expect `weakly_canonical()` to return `/a/b/` in cases (1) and (2) and `/` in cases (3) and (4) and this is what happens on other platforms.  
  
### Environment  
I tested this on Linux (Arch Linux and Ubuntu 20.04), macOS 10.15 and Windows 10.  
  
The Windows is the only platform where I get an exception. I checked boost 1.74.0 and 1.76.0. The environment is a Windows container running on CircleCI and reporting this version:  
```  
OS Name:                   Microsoft Windows Server 2019 Datacenter  
OS Version:                10.0.17763 N/A Build 17763  
```  
  
### Repro  
Here's a simple program I used to test this:  
```c++  
#include <array>  
#include <iostream>  
#include <boost/filesystem.hpp>  
  
using namespace std;  
using namespace boost::filesystem;  
  
int main()  
{  
    array<path, 4> samples = {  
        "/a/../a/b/",  
        "a/../a/b/",  
        "/../a/..",  
        "/../../a/../",  
    };  
    for (path const& sample: samples)  
    {  
        cout << "Path: " << sample << "; ";  
        try  
        {  
            cout << "weakly_canonical = " << weakly_canonical(sample) << endl;  
        }  
        catch (exception const& error)  
        {  
            cout << "EXCEPTION: " << error.what() << endl;  
        }  
    }  
    return 0;  
}  
  
```  
  
#### Output on Windows  
```  
Path: "/a/../a/b/"; weakly_canonical = EXCEPTION: boost::filesystem::weakly_canonical: The system cannot find the file specified  
Path: "a/../a/b/"; weakly_canonical = EXCEPTION: boost::filesystem::weakly_canonical: The system cannot find the file specified  
Path: "/../a/.."; weakly_canonical = EXCEPTION: boost::filesystem::weakly_canonical: The system cannot find the file specified  
Path: "/../../a/../"; weakly_canonical = EXCEPTION: boost::filesystem::weakly_canonical: The system cannot find the file specified  
```  
#### Output on other platforms  
```  
Path: "/a/../a/b/"; weakly_canonical = "/a/b/."  
Path: "a/../a/b/"; weakly_canonical = "a/b/."  
Path: "/../a/.."; weakly_canonical = "/"  
Path: "/../../a/../"; weakly_canonical = "/."  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-07-28 17:01:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/201#issuecomment-888471293  

Thanks for the report.

---

## Comment 2

> Username: cameel  
> Created at: 2021-07-28 17:23:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/201#issuecomment-888485144  

That was fast. Thanks!
