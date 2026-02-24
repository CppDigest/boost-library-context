# #26 - Boost top-level installation broken when used as external project since version 1.78 (regression from 1.77) [Closed]

> Username: mojca  
> Created at: 2022-10-20 15:27:45 UTC  
> Updated at: 2023-01-02 18:31:38 UTC  
> Closed at: 2023-01-02 18:31:38 UTC  
> Url: https://github.com/boostorg/cmake/issues/26  

I have prepared a "minimal example" project to reproduce the problem:  
* https://github.com/mojca/cmake-troubleshooting-example/blob/main/external/boost/CMakeLists.txt  
  
When using boost 1.77 as an external project via `FetchContent`, it works correctly. However version 1.78 and later (I tested 1.80) fail during `cmake --install .` with  
  
```  
$ DESTDIR=/tmp/foo cmake --install .  
-- Install configuration: ""  
CMake Error at cmake_install.cmake:47 (include):  
  include could not find requested file:  
  
    /path/to/cmake-troubleshooting-example/build/_deps/boost-build/cmake_install.cmake  
```  
  
(Possibly broken by https://github.com/boostorg/cmake/commit/c2f52bfbb36422f7baf236e1bf7746f3b9be2590.)  
  
The above problem should count as a bug report. Everything else written below should count as a feature request.  
  
To be fair, I don't actually want to install Boost in my particular example. All I want is to compile some components as static libraries and include them directly into my project. With some static libraries compiled directly into the binary I no longer need or want to install boost itself.  
  
A dirty workaround to circumvent the above issue is to specify  
```  
set(CMAKE_SKIP_INSTALL_RULES ON)  
```  
just before `FetchContent_MakeAvailable(boost)`.  
  
Some other libraries helpfully provide an option to make it trivial to avoid installation:  
* https://github.com/fmtlib/fmt/blob/5ad7b713814f83389d45b92a23ccc08a2351f3f2/CMakeLists.txt#L280  
  
```cmake  
option(FMT_INSTALL "Generate the install target." ${FMT_MASTER_PROJECT})  
```  
  
It would be really great if something similar could be provided by boost as well, so that setting `BOOST_INSTALL` to `OFF` would skip the installation.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-20 15:52:41 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1285792501  

Do I need pybind in this example?

---

## Comment 2

> Username: mojca  
> Created at: 2022-10-20 15:55:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1285796656  

> Do I need pybind in this example?  
  
No, of course not. Feel free to comment it out. (I wanted to collect a bunch of different issues I'm experiencing in a single repository, and the one related to pybind is not even finished yet, but more importantly: completely unrelated to boost.)

---

## Comment 3

> Username: pdimov  
> Created at: 2022-10-20 16:04:16 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1285808436  

I wonder what's going on here. It's probably this line https://github.com/boostorg/cmake/blob/985b866e657ee5f981c4523ba02a8457c715fa33/include/BoostRoot.cmake#L93  
  
but I'm not sure why it causes it to fail in such a manner.  
  
I'll investigate.

---

## Comment 4

> Username: pdimov  
> Created at: 2022-10-20 16:40:19 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1285854617  

Apparently, setting CMAKE_SKIP_INSTALL_RULES to ON disables the generation of `cmake_install.cmake` for the subproject, but the parent `cmake_install.cmake` is still trying to include it.  
  
It looks like my use of CMAKE_SKIP_INSTALL_RULES is wrong, and that it's only possible to use that variable at the top level.

---

## Comment 5

> Username: mojca  
> Created at: 2022-10-20 16:58:32 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1285874875  

What if you define a variable `BOOST_INSTALL` that gets set to `ON` by default when building Boost directly, and to `OFF` when built as an external component, while still letting the user decide whether on not to install it nonetheless?

---

## Comment 6

> Username: pdimov  
> Created at: 2022-10-21 04:19:51 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286438891  

Should be fixed by https://github.com/boostorg/cmake/commit/4c755a74042f096f94bc134b517fdf8f29f4aaf3.

---

## Comment 7

> Username: mojca  
> Created at: 2022-10-21 05:44:28 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286488091  

Awesome, thank you very much, that seems to work.  
  
Is there any chance to update https://github.com/boostorg/boost/tree/master/tools as well and land these changes in boost 1.81.0?

---

## Comment 8

> Username: mojca  
> Created at: 2022-10-21 05:53:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286493097  

Unrelated, please let me know whether it makes sense to open a new ticket.  
  
I only wanted to use `filesystem`, nothing else. Yet I need to specify 32 modules to convince cmake to even successfully configure/generate the build files. Are all of these really hard dependencies that are strictly necessary to provide a functional `filesystem`, or is there any chance to strip down this list a bit in the future?  
```cmake  
        GIT_SUBMODULES  
                       tools/cmake  
                       libs/align  
                       libs/assert  
                       libs/atomic  
                       libs/concept_check  
                       libs/config  
                       libs/container_hash  
                       libs/conversion  
                       libs/core  
                       libs/detail  
                       libs/filesystem  
                       libs/function_types  
                       libs/fusion  
                       libs/integer  
                       libs/io  
                       libs/iterator  
                       libs/move  
                       libs/mp11  
                       libs/mpl  
                       libs/optional  
                       libs/predef  
                       libs/preprocessor  
                       libs/smart_ptr  
                       libs/static_assert  
                       libs/system  
                       libs/throw_exception  
                       libs/tuple  
                       libs/type_traits  
                       libs/typeof  
                       libs/utility  
                       libs/variant2  
                       libs/winapi  
```

---

## Comment 9

> Username: pdimov  
> Created at: 2022-10-21 12:06:28 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286873637  

> Is there any chance to update https://github.com/boostorg/boost/tree/master/tools as well and land these changes in boost 1.81.0?  
  
Yeah, I was waiting for CI to catch up.

---

## Comment 10

> Username: pdimov  
> Created at: 2022-10-21 12:17:32 UTC  
> Updated at: 2022-10-21 12:18:05 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286884815  

Ironically, with I tested your project with the current `develop` branch to see if it's fixed, it turned out that you need a few more submodules:  
```  
+                       libs/functional  
+                       libs/function  
+                       libs/bind  
+                       libs/type_index  
```  
Looks like Fusion has acquired a dependency on Functional in the meantime.  
  
Trimming the Boost interdependencies is an ongoing project. The main problem is that most of the users (all until recently) acquired Boost as a whole so the dependencies weren't an issue for them, so it's easy to introduce a new dependency in an existing library without anything breaking or anyone complaining, or anyone even noticing (unless this introduces a dependency cycle, but even then.) (It was a lot of work just eliminating cycles.)  
  
For the specific case of Filesystem, you can see what it uses here:  
  
https://pdimov.github.io/boostdep-report/develop/filesystem.html  
  
The main problem is the use of Iterator which brings in a lot of stuff transitively, such as e.g. Fusion.  
  
(https://pdimov.github.io/boostdep-report has more interesting pages to look at.)  
  
The CMake infrastructure (i.e. this repo) can't do much about that. It's up to each individual library to pursue the task of minimizing dependencies (or its "weight" as the dependency report calls it.)  
  
So the way to make Filesystem "lighter" would be to either open an issue there and ask its maintainer (@lastique) to do something about it, or subscribe to the Boost mailing list and raise the topic there.

---

## Comment 11

> Username: Lastique  
> Created at: 2022-10-21 12:49:56 UTC  
> Updated at: 2022-10-21 12:52:38 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286919419  

FWIW, I do not view "reducing the number of dependencies" as a goal to pursue. If some functionality in my library is implemented by another Boost library, I will use it. Because that's what libraries are for. So, if you're about to ask me to remove the dependency on Boost.Iterator from Boost.Filesystem, please don't bother as that would require me to duplicate a significant portion of Boost.Iterator in Boost.Filesystem and I'm not going to do this.  
  
We can consider doing something about Boost.Iterator though. Like splitting the core utilities to Boost.IteratorCore or something.

---

## Comment 12

> Username: pdimov  
> Created at: 2022-10-21 13:17:49 UTC  
> Updated at: 2022-10-21 13:18:26 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1286950339  

The problem here is again that Iterator has a core part that other libraries want to use (`iterator_facade` and `iterator_adaptor`) and a "useful utilities" part that are generally for end users and from where all these secondary dependencies come from.  
  
edit: didn't see the edit before replying :-)

---

## Comment 13

> Username: mojca  
> Created at: 2022-11-11 15:32:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/26#issuecomment-1311830988  

I also opened https://github.com/boostorg/release-tools/pull/35, hoping that the top-level `CMakeLists.txt` could be included in the official tarballs. Cloning from git "takes forever", fetching from a tarball is much faster.
