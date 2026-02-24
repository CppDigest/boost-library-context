# #173 - BOOST_UUID_LINK_LIBATOMIC does not link atomic statically if Boost_USE_STATIC_LIBS is ON [Closed]

> Username: Yepadee  
> Created at: 2025-02-17 14:17:55 UTC  
> Updated at: 2025-02-17 17:46:22 UTC  
> Closed at: 2025-02-17 17:46:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/173  

When I use Boost_USE_STATIC_LIBS with boost uuid it forces me to link libatomic dynamically. Would it be possible to have libatomic statically linked when Boost_USE_STATIC_LIBS is on?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-02-17 15:07:59 UTC  
> Url: https://github.com/boostorg/uuid/issues/173#issuecomment-2663393898  

How do you use the library?

---

## Comment 2

> Username: Yepadee  
> Created at: 2025-02-17 17:26:01 UTC  
> Updated at: 2025-02-17 17:30:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/173#issuecomment-2663724760  

I have boost added as a submodule that builds with my project, and link boost_uuid to my target in cmake. Compiler im using is gcc14

---

## Comment 3

> Username: pdimov  
> Created at: 2025-02-17 17:35:38 UTC  
> Url: https://github.com/boostorg/uuid/issues/173#issuecomment-2663750144  

In that case you can set `BOOST_UUID_LINK_LIBATOMIC` to `OFF` before the `add_subdirectory` call that brings in Boost, and then if needed use `target_link_libraries` on `boost_uuid` to link it with the appropriate static `libatomic` library by hand.  
  
The `Boost_USE_STATIC_LIBS` option is not intended to control static/dynamic linking to external libraries, only to (installed) Boost ones, and it doesn't really apply to the `add_subdirectory` case, which is governed by `BUILD_SHARED_LIBS`.  
  
We can in principle add a separate option for statically linking to libatomic, but I'm not quite sure what it needs to do. At the moment the CMakeLists.txt file just does `target_link_libraries(boost_uuid INTERFACE atomic)` without specifying static or dynamic.

---

## Comment 4

> Username: Yepadee  
> Created at: 2025-02-17 17:37:39 UTC  
> Url: https://github.com/boostorg/uuid/issues/173#issuecomment-2663758897  

Okay thank you! I'm doing this currently as a work around - do you know why it was wanting to link libatomic to begin with if it is seemingly fine without it?

---

## Comment 5

> Username: pdimov  
> Created at: 2025-02-17 17:43:47 UTC  
> Url: https://github.com/boostorg/uuid/issues/173#issuecomment-2663774384  

libatomic is required if you use the time-based UUID generators (time_generator_v1 and time_generator_v6.)

---

## Comment 6

> Username: Yepadee  
> Created at: 2025-02-17 17:46:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/173#issuecomment-2663778872  

Excellent, thank you for clarifying! I'll mark this as closed now
