# #10 - Nonexistent Boost config macro BOOST_NO_CXX11_HDR_MEMORY [Closed]

> Username: jefftrull  
> Created at: 2020-09-04 05:04:59 UTC  
> Updated at: 2021-03-10 07:18:32 UTC  
> Closed at: 2021-03-10 07:18:32 UTC  
> Url: https://github.com/boostorg/container_hash/issues/10  

An automated search for erroneous macro usage picked this up here and in Wave. There is no such macro because `<memory>` predates C++11, so the test in `extensions.hpp` is always true.

---

## Comment 1

> Username: mclow  
> Created at: 2020-09-11 03:09:58 UTC  
> Url: https://github.com/boostorg/container_hash/issues/10#issuecomment-690846790  

Created https://github.com/boostorg/container_hash/pull/11 to address this.

---

## Comment 2

> Username: jefftrull  
> Created at: 2021-03-10 07:18:32 UTC  
> Url: https://github.com/boostorg/container_hash/issues/10#issuecomment-795008018  

Looks like this was resolved.
