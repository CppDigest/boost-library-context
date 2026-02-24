# #116 - Quickbook no longer builds [Closed]

> Username: pdimov  
> Created at: 2019-08-05 09:18:15 UTC  
> Updated at: 2019-08-06 09:24:29 UTC  
> Closed at: 2019-08-06 09:24:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116  

The superproject builds (https://travis-ci.org/boostorg/boost/builds) have been broken, starting with https://travis-ci.org/boostorg/boost/builds/567103808; the error is  
  
```  
tools/quickbook/src/include_paths.cpp: In function ‘void quickbook::include_search_glob(std::set<quickbook::quickbook_path>&, const quickbook::quickbook_path&, std::string, quickbook::state&)’:  
tools/quickbook/src/include_paths.cpp:160:14: error: ‘directory_iterator’ is not a member of ‘quickbook::fs’  
         for (fs::directory_iterator dir_i(base_dir), dir_e; dir_i != dir_e;  
              ^  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2019-08-05 15:06:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116#issuecomment-518272947  

The fix is probably to add #include <filesystem/directory.hpp> or not   
define BOOST_FILESYSTEM_NO_DEPRECATED.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-08-05 15:50:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116#issuecomment-518290614  

Sounds like it's missing `#include <boost/filesystem/directory.hpp>`, yes. The granular headers don't seem to be documented; is this a supported use case?

---

## Comment 3

> Username: pdimov  
> Created at: 2019-08-05 18:22:08 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116#issuecomment-518344284  

```  
/home/travis/boost/tools/quickbook/src/include_paths.cpp:15:42: fatal error: boost/filesystem/directory.hpp: No such file or directory  
#include <boost/filesystem/directory.hpp>  
                                         ^  
```  
  
Has this header been newly added?

---

## Comment 4

> Username: Lastique  
> Created at: 2019-08-05 19:04:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116#issuecomment-518359053  

Yes, it is supported now, it will be released in 1.72. You can include   
filesystem.hpp for now if you want universal code.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-08-05 19:04:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116#issuecomment-518359074  

Yes, it's in develop only for now.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-08-06 09:24:28 UTC  
> Url: https://github.com/boostorg/filesystem/issues/116#issuecomment-518588314  

Seems fine now after https://github.com/boostorg/quickbook/commit/60ecc01fc79eb61beda58172ae3939747df60061.
