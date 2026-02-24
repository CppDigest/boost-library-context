# #90 - Boost directory iterator not working with root dirs ie D:\ throws access denied. [Closed]

> Username: kenkit  
> Created at: 2018-11-16 12:55:15 UTC  
> Updated at: 2020-04-22 08:23:05 UTC  
> Closed at: 2020-04-18 14:11:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90  

I had originally posted the issue on the boost repo   
I'll just link it here https://github.com/boostorg/boost/issues/217

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-16 13:57:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-439400434  

Please, describe what the problem is and provide a small reproducer code, if possible. Also, what Boost and compiler are you using?

---

## Comment 2

> Username: kenkit  
> Created at: 2018-11-19 21:04:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-440040955  

Sorry I was offline for a while back there.  
But this is thee message I had posted originally.  
I've experienced the above bug on windows x86 using latest boost.  
  
The said code is below  
`  for (boost::filesystem::recursive_directory_iterator end,  
         dir(path(file_name));  
         dir != end; ++dir) `  
I was initially using ` dir(file_name);` when I discovered this.  
The error occured only when scanning root drive. ie `F:\ `  
  
I cannot get the list of files in a virtual hard drive on windows platform, the program would crash.  
But on normal drive it worked just fine.

---

## Comment 3

> Username: kenkit  
> Created at: 2018-11-20 09:25:57 UTC  
> Updated at: 2018-11-20 09:26:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-440202767  

Here is a complete code.  
I'm using ` Boost version: 1.67.0`  
  
```  
#include <boost/filesystem.hpp>  
#include <iostream>  
  
void grab_files(std::string file_path) {  
  
  using namespace boost::filesystem;  
  
  for (boost::filesystem::recursive_directory_iterator end, dir(file_path);  
       dir != end; ++dir) {  
  
      std::cout << "Found file:" << dir->path() << std::endl;  
      
  }  
}  
int main(int argc, char **argv) {  
  
  grab_files("D:\\");  
  
  return 0;  
}  
```

---

## Comment 4

> Username: kenkit  
> Created at: 2020-04-18 13:16:13 UTC  
> Updated at: 2020-04-18 13:18:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-615869234  

Hi it looks as if the problem occurs when there are characters with chinese filenames or wide character types of contents in a directory.  
Any help to solve this will be greately appreciated

---

## Comment 5

> Username: Lastique  
> Created at: 2020-04-18 13:56:55 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-615876050  

You still haven't described what the problem is.

---

## Comment 6

> Username: kenkit  
> Created at: 2020-04-18 14:05:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-615877252  

I've discovered what the problem is, although I'm not sure if it is possible to implement a workaround.  
When directory iterator is scanning the root dir it comes across the recycle bin and other windows directory which it does not have read access to so it throws an access denied error.  
I've  tested and confirmed this problem using std::filesystem.  
Is there a way to skip this types of files ?  
Try running the above code on a root drive, the error is not triggered on some removable drives.

---

## Comment 7

> Username: kenkit  
> Created at: 2020-04-18 14:11:27 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-615878181  

I think I have an idea about how to fix this.  
Wrap the directory iterator under a catch statement, then if it fails, skip this directory.

---

## Comment 8

> Username: kenkit  
> Created at: 2020-04-18 14:12:10 UTC  
> Updated at: 2020-04-18 14:12:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-615878286  

I think a better solution would be for filesystem to skip this internally but still return the directory/file name.

---

## Comment 9

> Username: Lastique  
> Created at: 2020-04-18 14:46:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-615883233  

You can pass `directory_options::skip_permission_denied` to `recursive_directory_iterator` constructor to avoid recursing into directories to which you don't have permission.

---

## Comment 10

> Username: kenkit  
> Created at: 2020-04-22 08:23:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/90#issuecomment-617629391  

Wow, thank you soo much. This bug has been existing in my project for an year now.
