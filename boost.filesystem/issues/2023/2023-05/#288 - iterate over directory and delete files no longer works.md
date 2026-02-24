# #288 - iterate over directory and delete files no longer works [Closed]

> Username: captain-igloo  
> Created at: 2023-05-31 02:34:44 UTC  
> Updated at: 2023-06-06 10:26:08 UTC  
> Closed at: 2023-06-05 00:05:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/288  

I am using boost::filesystem to loop over files in a directory and conditionally delete them. It works in boost 1.71 but in boost 1.74 it crashes after the first file has been deleted. Is this a bug or am I doing something wrong? What is the recommended way to iterate over a directory and delete files?  
  
```  
$ git clone https://github.com/captain-igloo/boost-filesystem-bug.git  
  
$ cd boost-filesystem-bug  
  
$ docker build -t focal  -f focal.Dockerfile  .  
  
$ docker build -t jammy  -f jammy.Dockerfile  .  
  
# boost 1.71, files deleted  
$ docker run -it focal /test-boost/a.out  
"/tmp/b"  
filesystem::recursive_directory_iterator directory error: No such file or directory  
"/tmp/a"  
filesystem::recursive_directory_iterator directory error: No such file or directory  
"/tmp/c"  
filesystem::recursive_directory_iterator directory error: No such file or directory  
  
# boost 1.74, first file deleted, then crashes  
$ docker run -it jammy  /test-boost/a.out  
"/tmp/b"  
filesystem::recursive_directory_iterator increment error: No such file or directory  
a.out: /usr/include/boost/filesystem/directory.hpp:534: void boost::filesystem::recursive_directory_iterator::disable_recursion_pending(bool): Assertion `(!is_end())&&("disable_recursion_pending() on end recursive_directory_iterator")' failed.  
```  
  
main.cpp:  
```  
#include <iostream>  
#include <boost/filesystem.hpp>  
  
int main() {  
    boost::filesystem::path path("/tmp");  
    boost::filesystem::recursive_directory_iterator it(path), ite;  
    while(it != ite) {  
        if (!boost::filesystem::is_directory(*it)) {  
            std::cout << it->path() << std::endl;  
            boost::filesystem::remove(it->path());  
        }  
        try {  
            ++it;  
        } catch(std::exception& e) {  
            std::cout << e.what() << std::endl;  
            it.no_push();  
            ++it;  
        }  
    }  
    return 0;  
}  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2023-06-03 18:00:09 UTC  
> Updated at: 2023-06-06 10:26:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/288#issuecomment-1575100764  

I don't think this code is correct. `recursive_directory_iterator` must query the file type on every increment, which means the file must be present on the filesystem. Effectively, this is the same as if some other process removed the file while directory iteration was in progress, and this counts as a filesystem race.  
  
The fixed code would increment the iterator before removing the file. Additionally, if the increment throws, the iterator may become an end iterator, which you should check in the `catch` block.  
  
```  
#include <iostream>  
#include <boost/filesystem.hpp>  
  
int main() {  
    boost::filesystem::path path("test");  
    boost::filesystem::recursive_directory_iterator it(path), ite;  
    while(it != ite) {  
        boost::filesystem::path p = it->path();  
        try {  
            ++it;  
        } catch(std::exception& e) {  
            std::cout << e.what() << std::endl;  
            if (it == ite)  
                break;  
            it.disable_recursion_pending();  
            ++it;  
        }  
        if (!boost::filesystem::is_directory(p)) {  
            std::cout << p << std::endl;  
            boost::filesystem::remove(p);  
        }  
    }  
    return 0;  
}  
```  
  
That said, the original code does not *have* to fail because the implementation is *allowed* to cache some information in the `directory_entry`, if it is available during iteration without extra effort. That is, if the file type becomes available as a side effect of directory iteration, `recursive_directory_iterator` may reuse that information without having to query the filesystem. And in fact, that information is partially available on some filesystems and when glibc is used - `directory_iterator` does cache the file type in `directory_entry` it returns. Unfortunately, the permissions are not available, and only the symlink file type is available, so `recursive_directory_iterator` ends up querying the filesystem anyway. This is something I can fix, if only to improve performance. But I want to make it clear that this information is not available universally (i.e. some filesystems may not report anything at all and on some OSes/libcs this API is entirely missing), which means this behavior may vary between systems and filesystems and cannot be relied upon.

---

## Comment 2

> Username: captain-igloo  
> Created at: 2023-06-06 01:19:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/288#issuecomment-1577756083  

Thanks a lot for your help.  I have changed my code to increment the iterator first and it works nicely now.
