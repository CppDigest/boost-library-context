# #188 - Filesystem no longer supports `threading=single` [Closed]

> Username: pdimov  
> Created at: 2021-05-19 21:47:56 UTC  
> Updated at: 2021-05-19 23:46:20 UTC  
> Closed at: 2021-05-19 23:39:13 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188  

I'm seeing a regression in boost_install (https://travis-ci.org/github/boostorg/boost_install/jobs/771706182) that's likely caused by Filesystem adopting Atomic as a dependency, with the latter not supporting `threading=single` builds due to https://github.com/boostorg/atomic/blob/08bd4e20338c503d2acfdddfdaa8f5e0bcf9006c/build/Jamfile.v2#L28.  
  
In the real world this is going to affect Homebrew who for some reason ship both `threading=single` and `threading=multi` builds of Boost, I'm not sure why.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-05-19 21:54:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188#issuecomment-844501380  

I suppose, I could fix this for the Boost.Build side of things by checking `threading=multi`. But how do I do this in CMakeLists.txt? Is there a CMake variable that is similar to `threading` feature?

---

## Comment 2

> Username: pdimov  
> Created at: 2021-05-19 22:02:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188#issuecomment-844511734  

It's not related to CMakeLists.txt. This is because `b2 threading=single,multi install` builds both `libfilesystem` and `libfilesystem-mt`, but only `libatomic-mt`. The CMake config file that `b2` creates for `libfilesystem` tries to link to `libatomic`, but it's not there.  
  
The easiest way to avoid it is to just drop the `<threading>multi` requirement from `libs/atomic/build/Jamfile`. There should be no harm in using Atomic from a single-threaded library.  
  
Although I'm not sure what'll happen if Atomic tries to use e.g. pthread_mutex_lock with `threading=single`.

---

## Comment 3

> Username: Lastique  
> Created at: 2021-05-19 22:10:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188#issuecomment-844530166  

I understand that the problem you're having is with Boost.Build, but in order to retain support for single-threaded builds, changes must be made to all three of Jamfile, Filesystem source code and CMakeLists.txt (the latter - to keep it actual and in parity with Boost.Build). Hence my question about the CMake variable.  
  
> Although I'm not sure what'll happen if Atomic tries to use e.g. pthread_mutex_lock with `threading=single`.  
  
`threading=multi` does cause `-pthread` etc. added to the command line, so yes, that would break.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-05-19 23:13:41 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188#issuecomment-844558458  

But there's no problem with building Filesystem at present - it does build with `threading=single`, and produces a library. Or are you thinking about removing the dependency on Atomic when single threaded?  
  
> threading=multi does cause -pthread etc. added to the command line, so yes, that would break.  
  
Not necessarily - there are weak stub definitions of pthread_mutex_lock et al somewhere so it "works" without -pthread and does nothing, although I'm not sure on which platforms and starting with which version of what. :-)

---

## Comment 5

> Username: Lastique  
> Created at: 2021-05-19 23:24:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188#issuecomment-844563093  

> Or are you thinking about removing the dependency on Atomic when single threaded?  
  
Yes, isn't this what you were asking? It's also the logical thing to do.  
  
> Not necessarily...  
  
There are also different MSVC runtimes, I think. I don't really want to drop `threading=multi` in Boost.Atomic, that doesn't look right.  
  
It looks like there is no CMake variable similar to `threading=multi` in Boost.Build, at least I didn't find it. There is `Boost_USE_MULTITHREADED`, but it seems like it only affects the library names to link with in the user's projects, not when building Boost itself. Which begs the question, how does one build single-thread/multithreaded Boost with CMake?

---

## Comment 6

> Username: pdimov  
> Created at: 2021-05-19 23:46:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/188#issuecomment-844571748  

No, there's no equivalent of `threading=` in CMake. There, if your library uses threads, you do  
```  
set(THREADS_PREFER_PTHREAD_FLAG ON)  
find_package(Threads REQUIRED)  
```  
and then link to `Threads::Threads`. You don't do this in the Atomic CMakeLists.txt file (and I'm not sure you need to.)  
  
> There are also different MSVC runtimes, I think.  
  
Not since MSVC 7.1. It's only multithreaded now. But that wouldn't have mattered because it wouldn't have broken anything anyway.  
  
> Which begs the question, how does one build single-thread/multithreaded Boost with CMake?  
  
There's no way. I don't think there's any official CMake way to control whether something is built threaded or not, apart from the usual toolchain hacks, and there's no way to _detect_ whether threading support is enabled, either (so the superproject has no idea whether to add -mt to the library names, so it always does.) At the moment my working assumption is that CMake builds are always multithreaded. This was _almost_ the case with b2 builds too, but then it turned out that Homebrew builds with `threading=single`. No idea why they do.
