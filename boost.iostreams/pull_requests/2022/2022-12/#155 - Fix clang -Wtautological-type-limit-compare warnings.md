# #155 Fix clang -Wtautological-type-limit-compare warnings. [Open]

> Username: Romain-Geissler-1A  
> Created at: 2022-12-27 16:20:22 UTC  
> Updated at: 2023-01-17 08:53:54 UTC  
> Url: https://github.com/boostorg/iostreams/pull/155  

```  
In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/filtering_streambuf.hpp:17: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/chain.hpp:28: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/push.hpp:24: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/resolve.hpp:19: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/adapter/mode_adapter.hpp:24: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/operations.hpp:16: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/close.hpp:19: In file included from /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/detail/adapter/non_blocking_adapter.hpp:13: /data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/seek.hpp:86:20: error: result of comparison 'boost::iostreams::stream_offset' (aka 'long') > 9223372036854775807 is always  
 false [-Werror,-Wtautological-type-limit-compare]  
               off > integer_traits<std::streamoff>::const_max ) )  
               ~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/seek.hpp:85:20: error: result of comparison 'boost::iostreams::stream_offset' (aka 'long') < -9223372036854775808 is always false [-Werror,-Wtautological-type-limit-compare]  
             ( off < integer_traits<std::streamoff>::const_min ||  
               ~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/seek.hpp:113:20: error: result of comparison 'boost::iostreams::stream_offset' (aka 'long') > 9223372036854775807 is always false [-Werror,-Wtautological-type-limit-compare]  
               off > integer_traits<std::streamoff>::const_max ) )  
               ~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/data/mwrep/res/osp/Boost/23-0-0-0/include/boost/iostreams/seek.hpp:112:20: error: result of comparison 'boost::iostreams::stream_offset' (aka 'long') < -9223372036854775808 is always false [-Werror,-Wtautological-type-limit-compare]  
             ( off < integer_traits<std::streamoff>::const_min ||  
               ~~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
4 errors generated.  
```  
  
Note: I am not sure this check on the offset is valid in the first place, I don't know how possible it is that `stream_offset` is a different type than `std::streamoff` in which case this check is totally pointless as always false.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-17 00:22:17 UTC  
> Url: https://github.com/boostorg/iostreams/pull/155#issuecomment-1384693130  

`stream_offset` is defined as `boost::intmax_t` here: https://github.com/boostorg/iostreams/blob/4d2e763ba8b88f3350fbea29b54507a3f5adf0f0/include/boost/iostreams/positioning.hpp#L36

---

## Comment 2

> Username: mclow  
> Created_at: 2023-01-17 03:09:32 UTC  
> Url: https://github.com/boostorg/iostreams/pull/155#issuecomment-1384779000  

`std::streamoff` is implementation defined; usually `long long`.  See [Cpp Reference](https://en.cppreference.com/w/cpp/io/streamoff)  
  
What platform (compiler, standard library) are you using?

---

## Comment 3

> Username: Romain-Geissler-1A  
> Created_at: 2023-01-17 08:53:54 UTC  
> Url: https://github.com/boostorg/iostreams/pull/155#issuecomment-1385043464  

This happened with clang 15 (so a release version) which was using glibc headers (the future 2.37 release, I think when I posted this pull request I used a snapshot of the master branch as of mid december) and libstdc++ headers (the future gcc 13 release, also a snapshot as of mid december).

---
