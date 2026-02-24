# #469 - Boost.Process V2 doesn't compile with musl library [Open]

> Username: o01eg  
> Created at: 2025-03-21 17:52:08 UTC  
> Updated at: 2026-02-20 04:29:56 UTC  
> Url: https://github.com/boostorg/process/issues/469  

If I try to build code which works with glibc https://github.com/freeorion/freeorion/pull/5156 it failes to build on Void linux with musl library:  
  
```  
2025-03-11T03:42:09.7575013Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp: In function 'void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)':  
2025-03-11T03:42:09.7576711Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:128:15: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-11T03:42:09.7578069Z   128 |             ::close_range(0, whitelist.front() - 1, CLOSE_RANGE_UNSHARE);  
2025-03-11T03:42:09.7578554Z       |               ^~~~~~~~~~~  
2025-03-11T03:42:09.7578891Z       |               copy_range  
2025-03-11T03:42:09.7579757Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:138:19: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-11T03:42:09.7580810Z   138 |                 ::close_range(mine + 1, next - 1, CLOSE_RANGE_UNSHARE);  
2025-03-11T03:42:09.7581305Z       |                   ^~~~~~~~~~~  
2025-03-11T03:42:09.7581672Z       |                   copy_range  
2025-03-11T03:42:09.7582604Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:142:11: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-11T03:42:09.7583836Z   142 |         ::close_range(whitelist.back() + 1, std::numeric_limits<int>::max(), CLOSE_RANGE_UNSHARE);  
2025-03-11T03:42:09.7584466Z       |           ^~~~~~~~~~~  
2025-03-11T03:42:09.7584805Z       |           copy_range  
2025-03-11T03:42:09.7585693Z /usr/include/boost/process/v2/posix/detail/close_handles.ipp:145:11: error: '::close_range' has not been declared; did you mean 'copy_range'?  
2025-03-11T03:42:09.7586932Z   145 |         ::close_range(0, std::numeric_limits<int>::max(), CLOSE_RANGE_UNSHARE);  
2025-03-11T03:42:09.7587491Z       |           ^~~~~~~~~~~  
2025-03-11T03:42:09.7587829Z       |           copy_range  
```

---

## Comment 1

> Username: striezel  
> Created at: 2025-08-27 18:35:00 UTC  
> Updated at: 2025-08-27 18:35:29 UTC  
> Url: https://github.com/boostorg/process/issues/469#issuecomment-3229319330  

I encountered a similar / the same issue on Alpine Linux v3.22.1, which also uses [musl libc](https://musl.libc.org/) (currently version 1.25.0-r10) and Boost 1.84.0:  
  
```  
/usr/include/boost/process/v2/posix/detail/close_handles.ipp: In function 'void boost::process::v2::posix::detail::close_all(const std::vector<int>&, boost::system::error_code&)':  
/usr/include/boost/process/v2/posix/detail/close_handles.ipp:128:15: error: '::close_range' has not been declared; did you mean 'close_all'?  
  128 |             ::close_range(0, whitelist.front() - 1, CLOSE_RANGE_UNSHARE);  
      |               ^~~~~~~~~~~  
      |               close_all  
/usr/include/boost/process/v2/posix/detail/close_handles.ipp:138:19: error: '::close_range' has not been declared; did you mean 'close_all'?  
  138 |                 ::close_range(mine + 1, next - 1, CLOSE_RANGE_UNSHARE);  
      |                   ^~~~~~~~~~~  
      |                   close_all  
/usr/include/boost/process/v2/posix/detail/close_handles.ipp:142:11: error: '::close_range' has not been declared; did you mean 'close_all'?  
  142 |         ::close_range(whitelist.back() + 1, std::numeric_limits<int>::max(), CLOSE_RANGE_UNSHARE);  
      |           ^~~~~~~~~~~  
      |           close_all  
/usr/include/boost/process/v2/posix/detail/close_handles.ipp:145:11: error: '::close_range' has not been declared; did you mean 'close_all'?  
  145 |         ::close_range(0, std::numeric_limits<int>::max(), CLOSE_RANGE_UNSHARE);  
      |           ^~~~~~~~~~~  
      |           close_all  
```  
  
I worked around that issue by just not using Boost Process v2 on Alpine, but that is less than ideal.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-08-28 09:58:56 UTC  
> Url: https://github.com/boostorg/process/issues/469#issuecomment-3232817573  

Can you check against the current version?

---

## Comment 3

> Username: o01eg  
> Created at: 2026-02-20 04:29:56 UTC  
> Url: https://github.com/boostorg/process/issues/469#issuecomment-3931578675  

Void with boost 1.90 is fixed, Alpine still uses broken 1.84.
