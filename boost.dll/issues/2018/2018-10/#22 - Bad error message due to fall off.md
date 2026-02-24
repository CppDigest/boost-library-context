# #22 - Bad error message due to fall off [Closed]

> Username: Firefly35  
> Created at: 2018-10-31 08:45:17 UTC  
> Updated at: 2019-01-05 10:57:19 UTC  
> Closed at: 2019-01-05 10:54:26 UTC  
> Url: https://github.com/boostorg/dll/issues/22  

Hello,  
using boost::dll in my XPCF dynamic component framework (boost::dll is used in https://github.com/b-com-software-basis repository xpcf in the ModuleManager.cpp file), a module (i.e. a shared library) developer has met the following issue :  
- The instantiated component C inherits a I interface, in the component implementation, the declaration of a pure virtual method of I (declared as override in the component header) was not in the component:: scope  
- Dynamically loading the module failed and the output message from boost::dll is "boost::dll::shared_library::load() failed (dlerror system message: .../shared/debug/module: cannot open shared object file: No such file or directory): Bad file descriptor"  
  
The issue appeared on linux (on Mac, clang complains for missing symbols at the module link time, hence prevent the issue to append).  
  
Investigating inside shared_library_impl.hpp, it appears that errors (in this case dlerror = missing symbol) with load_mode = append_decorations are not handled in the scope.   
Leaving the append_decoration scope in error, there is a try to open the file with the specified path (which is incomplete as it misses the lib prefix and the .so suffix), that results in the erroneous error message "bad file descriptor".  
  
I'd expect boost::dll to check if the current error is a bad _file_descriptor, in such a case to try opening the exactly specified path. Otherwise, other errors should be returned immediately before the append_decorations scope ends.  
  
Or maybe, if append_decorations fails, boost should return the error without trying to open the exact specified path : it is the user responsibility to provide an abstract shared library name when used in conjunction with append_decorations ?  
  
Environment:   
g++ version is 7.3.0, boost 1.68 built with c++14 option on ubuntu 18.04.  
  
Best regards

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-01-05 10:54:26 UTC  
> Updated at: 2019-01-05 10:55:25 UTC  
> Url: https://github.com/boostorg/dll/issues/22#issuecomment-451645632  

This one should be already fixed in develop branch by #23   
Closing
