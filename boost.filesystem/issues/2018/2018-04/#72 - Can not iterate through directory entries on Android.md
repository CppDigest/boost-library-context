# #72 - Can not iterate through directory entries on Android [Closed]

> Username: alexeikh  
> Created at: 2018-04-10 15:35:52 UTC  
> Updated at: 2018-06-06 13:06:49 UTC  
> Closed at: 2018-06-06 13:06:49 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72  

### Prerequisites  
  
- Android device with Android OS below 6.0, for example Samsung Galaxy S6 with Android 5.0.2. The issue should be reproducible on Android Emulator as well.  
- Android NDK with enabled Unified Headers, for example version r16b  
- Compiler: Clang (shouldn't be important)  
- STL: GNU STL (shouldn't be important)  
- Selected (minimum) Android API level: 14 (shouldn't be important)  
  
### How to reproduce  
  
Compile the following sample code as Android executable.  
```c++  
#include <boost/filesystem.hpp>  
#include <iostream>  
  
int main() {  
    namespace fs = boost::filesystem;  
  
    fs::directory_iterator iter("/data/local/tmp");  
    for (const fs::path& p: iter)  
        std::cout << "Directory entry found: " << p.c_str() << std::endl;  
  
    return 0;  
}  
```  
  
Copy the resulting executable to the device and run it:  
```sh  
adb push libs/armeabi-v7a/sample-cmdline-app /data/local/tmp/  
adb shell /data/local/tmp/sample-cmdline-app  
```  
  
### Expected behavior  
  
All directory entries are listed, for example:  
```  
Directory entry found: /data/local/tmp/sample-cmdline-app  
Directory entry found: /data/local/tmp/some-another-file.txt  
```  
  
### Actual behavior  
  
`boost::filesystem::filesystem_error` is thrown on the second iteration of the loop:  
```  
Directory entry found: /data/local/tmp/sample-cmdline-app  
terminate called after throwing an instance of 'boost::filesystem::filesystem_error'  
  what():  boost::filesystem::directory_iterator::operator++: Function not implemented: "/data/local/tmp"  
```

---

## Comment 1

> Username: alexeikh  
> Created at: 2018-04-10 15:40:52 UTC  
> Updated at: 2018-04-10 15:48:48 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72#issuecomment-380147423  

The cause of this issue seems to be buggy `readdir_r()` implementation on Android 5.x and below.   
  
If using an old Android NDK without Unified Headers, the issue does not happen, because `_POSIX_THREAD_SAFE_FUNCTIONS` is not defined and Boost.Filesystem choses to use `readdir()` instead of `readdir_r()`.

---

## Comment 2

> Username: alexeikh  
> Created at: 2018-04-10 15:48:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72#issuecomment-380150283  

Applying patch #68 fixes the issue.

---

## Comment 3

> Username: alexeikh  
> Created at: 2018-04-20 13:35:22 UTC  
> Updated at: 2018-04-20 13:46:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72#issuecomment-383097755  

The same issue is also reported on the Boost's Trac:  
https://svn.boost.org/trac10/ticket/13172  
  
There, another patch is proposed for the fix:  
https://gist.github.com/webmaster128/5912a70d100e9ef341df67b177c465d6  
  
I think, both #68 and `errno` resetting should be applied. Regarding the `errno` resetting, it may make sense to reset `errno` in 2 places: both before the `readdir_r()` call and before the `readdir()` call.  
  
@webmaster128 and @rcdailey, you may find it useful that #68 also fixes this issue.

---

## Comment 4

> Username: alexeikh  
> Created at: 2018-05-18 16:07:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72#issuecomment-390255280  

Pull request #51 implements exactly errno resetting in 2 places and also fixes this issue.

---

## Comment 5

> Username: rcdailey  
> Created at: 2018-05-18 18:57:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72#issuecomment-390301111  

How will we know which version of Boost this fix becomes available in?

---

## Comment 6

> Username: alexeikh  
> Created at: 2018-06-06 13:06:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/72#issuecomment-395061536  

The issue has been fixed by merging #51 in.
