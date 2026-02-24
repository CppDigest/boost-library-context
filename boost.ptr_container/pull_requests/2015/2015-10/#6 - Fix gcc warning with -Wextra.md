# #6 Fix gcc warning with -Wextra. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2015-10-28 23:48:42 UTC  
> Updated at: 2017-11-05 16:23:31 UTC  
> Merged at: 2017-11-05 16:23:31 UTC  
> Closed at: 2017-11-05 16:23:31 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/6  

Hi,  
gcc is emitting a warning in the file map_iterator.hpp when using -Wextra:  
  
include/boost/ptr_container/detail/map_iterator.hpp:52:48: error: type qualifiers ignored on function return type [-Werror=ignored-qualifiers]  
             const ref_pair\* const operator->() const  
                                                ^  
  
Indeed const on the pointer itself is not needed.  
  
Ok for the trunk ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2016-05-20 16:47:11 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/6#issuecomment-220658111  

Ping.

---

## Comment 2

> Username: Romain-Geissler-1A  
> Created_at: 2016-07-25 08:06:19 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/6#issuecomment-234877349  

Hi,  
  
Is this boost library still maintained ? I see almost no activity in the git history, and trivial patch aren't reviewed.  
  
Cheers,  
Romain

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2017-06-18 17:42:55 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/6#issuecomment-309291751  

Ping.

---
