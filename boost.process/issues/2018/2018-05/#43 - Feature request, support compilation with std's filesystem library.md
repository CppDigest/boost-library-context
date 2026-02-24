# #43 - Feature request, support compilation with std's filesystem library [Closed]

> Username: correaa  
> Created at: 2018-05-22 01:44:09 UTC  
> Updated at: 2018-05-26 18:32:18 UTC  
> Closed at: 2018-05-26 18:32:18 UTC  
> Url: https://github.com/boostorg/process/issues/43  

Boost.Process depends on Boost.Filesystem, mainly because of boost::filesystem::path.  
Now that Filesystem is in the standard (almost?) I think it would be nice to have the option for Boost.Process to compile against std's filesystem (`#include<experimental/filesystem>`) instead of `#include<boost/filesystem.hpp>`.

---

## Comment 1

> Username: egorpugin  
> Created at: 2018-05-22 08:35:25 UTC  
> Url: https://github.com/boostorg/process/issues/43#issuecomment-390908626  

See https://github.com/klemens-morgenstern/boost-process/issues/164

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2018-05-26 18:32:18 UTC  
> Url: https://github.com/boostorg/process/issues/43#issuecomment-392279519  

Closed bc duplicate.
