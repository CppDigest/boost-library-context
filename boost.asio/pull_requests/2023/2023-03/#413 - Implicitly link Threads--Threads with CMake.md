# #413 Implicitly link Threads::Threads with CMake [Open]

> Username: lordjaxom  
> Created at: 2023-03-29 12:59:22 UTC  
> Updated at: 2023-04-11 15:36:38 UTC  
> Url: https://github.com/boostorg/asio/pull/413  

This PR adds the Threads::Threads library (which resolves to pthread on Linux and is empty on Windows) when targeting the interface library Boost::asio. Without it, using Boost::asio on Linux leads to undefined symbols, e.g.  
  
set(Boost_ROOT <path_to_boost_install_dir>)  
find_package(Boost COMPONENTS asio)  
target_link_library(myexe Boost::asio)

---

## Comment 1

> Username: lordjaxom  
> Created_at: 2023-04-11 15:36:38 UTC  
> Url: https://github.com/boostorg/asio/pull/413#issuecomment-1503629300  

Submitting this here since CMake support is not a part of standalone asio.

---
