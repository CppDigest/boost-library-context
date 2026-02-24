# #17 Use Mac Sandbox compatible temp folder [Closed]

> Username: stheophil  
> Created at: 2015-03-19 13:56:14 UTC  
> Updated at: 2016-09-19 14:40:57 UTC  
> Closed at: 2016-09-19 14:40:57 UTC  
> Url: https://github.com/boostorg/interprocess/pull/17  

The `/tmp` folder is not accessible for sandboxed Applications on the Mac. The docs are clear that you either have to get the correct folder via the Mac APIs.   
  
My fix destroys the header-only nature of the library. But that is certainly better than forcing all files that include `shared_dir_helpers.hpp` to become Objective C++ files. Using /tmp is ok on the Mac if the application will not be distributed through the App Store **and** if the shared memory segment does not need to live for several days. `/tmp`  is automatically emptied every 3 days.  
  
I've seen several requests on Trac for other ways to get the shared memory folder (environment variables e.g.) `BOOST_INTERPROCESS_SHARED_DIR_PATH` also exists. I thought it may be better to provide a single but more general extension-point instead of adding more of them :-)  
  
Thus I've implemented an optional, user-implementable function that can return the correct temporary directory.

---
