# #342 - clang-15 error: expected unqualified-id due to dirfd macro in macOS 14 SDK [Closed]

> Username: k15tfu  
> Created at: 2023-10-05 11:28:59 UTC  
> Updated at: 2023-10-09 02:46:27 UTC  
> Closed at: 2023-10-09 02:46:27 UTC  
> Url: https://github.com/boostorg/process/issues/342  

Hi!  
  
In file boost/process/detail/posix/handles.hpp:  
```  
boost/process/detail/posix/handles.hpp:36:20: error: expected unqualified-id  
    auto my_fd = ::dirfd(dir.get());  
                   ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.0.sdk/usr/include/dirent.h:136:21: note: expanded from macro 'dirfd'  
#define dirfd(dirp) ({                         \  
                    ^  
In file included from [...]:  
In file included from boost/process/child.hpp:25:  
In file included from boost/process/posix.hpp:8:  
In file included from boost/process/detail/posix/fd.hpp:15:  
In file included from boost/process/detail/used_handles.hpp:14:  
boost/process/detail/posix/handles.hpp:36:20: error: unexpected type name 'DIR': expected expression  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.0.sdk/usr/include/dirent.h:137:5: note: expanded from macro 'dirfd'  
    DIR *_dirp = (dirp);                       \  
    ^  
In file included from [...]:  
In file included from boost/process/child.hpp:25:  
In file included from boost/process/posix.hpp:8:  
In file included from boost/process/detail/posix/fd.hpp:15:  
In file included from boost/process/detail/used_handles.hpp:14:  
boost/process/detail/posix/handles.hpp:36:20: error: use of undeclared identifier '_dirp'  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.0.sdk/usr/include/dirent.h:137:10: note: expanded from macro 'dirfd'  
    DIR *_dirp = (dirp);                       \  
         ^  
In file included from [...]:  
In file included from boost/process/child.hpp:25:  
In file included from boost/process/posix.hpp:8:  
In file included from boost/process/detail/posix/fd.hpp:15:  
In file included from boost/process/detail/used_handles.hpp:14:  
boost/process/detail/posix/handles.hpp:120:24: error: expected unqualified-id  
        auto my_fd = ::dirfd(dir);  
                       ^  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.0.sdk/usr/include/dirent.h:136:21: note: expanded from macro 'dirfd'  
#define dirfd(dirp) ({                         \  
                    ^  
In file included from [...]:  
In file included from boost/process/child.hpp:25:  
In file included from boost/process/posix.hpp:8:  
In file included from boost/process/detail/posix/fd.hpp:15:  
In file included from boost/process/detail/used_handles.hpp:14:  
boost/process/detail/posix/handles.hpp:120:24: error: unexpected type name 'DIR': expected expression  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.0.sdk/usr/include/dirent.h:137:5: note: expanded from macro 'dirfd'  
    DIR *_dirp = (dirp);                       \  
    ^  
In file included from [...]:  
In file included from boost/process/child.hpp:25:  
In file included from boost/process/posix.hpp:8:  
In file included from boost/process/detail/posix/fd.hpp:15:  
In file included from boost/process/detail/used_handles.hpp:14:  
boost/process/detail/posix/handles.hpp:120:24: error: use of undeclared identifier '_dirp'  
  
/Library/Developer/CommandLineTools/SDKs/MacOSX14.0.sdk/usr/include/dirent.h:137:10: note: expanded from macro 'dirfd'  
    DIR *_dirp = (dirp);                       \  
         ^  
6 errors generated.  
```
