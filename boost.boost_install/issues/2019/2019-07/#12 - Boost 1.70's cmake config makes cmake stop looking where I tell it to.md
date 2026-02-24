# #12 - Boost 1.70's cmake config makes cmake stop looking where I tell it to [Closed]

> Username: tonyelewis  
> Created at: 2019-07-04 15:23:07 UTC  
> Updated at: 2020-05-06 19:28:43 UTC  
> Closed at: 2020-05-06 19:28:43 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12  

Boost 1.70's cmake config makes `cmake` stop looking where I tell it to in two ways:  
  
1. With Boost 1.68, I could tell `cmake` the location of the Boost I wanted to use with either a `BOOST_ROOT` environment variable or a `-DBOOST_ROOT=[...]` command line flag but with 1.70.0, the latter option has stopped working.  
1. If I have a Boost 1.70 directory in `/opt/`, `cmake` uses that one instead of the Boost (either 1.68 or 1.70) in another location that I explicitly request (via either a `BOOST_ROOT` environment variable or a `-DBOOST_ROOT=[...]` command line flag). Even if I have two Boost 1.70 directories in `/opt/`, `cmake` chooses which one to use itself, disregarding my explicit choice.  
  
I have seen these issues with `cmake-3.12.1-Linux-x86_64` and `cmake-3.15.0-rc3-Linux-x86_64`.  
  
Apologies: I'm not sure whether this issue should be raised here or against `FindBoost.cmake` on https://gitlab.kitware.com/cmake &mdash; I'm guessing here because the problem appears to be associated with a change in the Boost version not the CMake version. Sorry if that's incorrect.

---

## Comment 1

> Username: tonyelewis  
> Created at: 2019-07-04 15:30:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508519851  

More info in case it helps&hellip;  
  
I'm testing with a `CMakeLists.txt` like this:  
  
~~~cmake  
cmake_minimum_required( VERSION 3.7 )  
  
project( cmake-boost-problems )  
  
find_package( Boost 1.68.0 REQUIRED program_options )  
  
add_executable( example_exe example_exe.cpp )  
  
target_link_libraries( example_exe Boost::program_options )  
~~~  
  
&hellip;and a C++ file like this:  
  
~~~cpp  
#include <iostream>  
  
#include <boost/program_options.hpp>  
  
using ::boost::program_options::notify;  
using ::boost::program_options::options_description;  
using ::boost::program_options::parse_command_line;  
using ::boost::program_options::store;  
using ::boost::program_options::value;  
using ::boost::program_options::variables_map;  
using ::std::cout;  
  
int main(int ac, char **av) {  
  ::std::cout << "Hello world\n";  
  
  // Declare the supported options.  
  options_description desc("Allowed options");  
  desc.add_options()("help", "produce help message")(  
      "compression", value<int>(), "set compression level");  
  
  variables_map vm;  
  store(parse_command_line(ac, av, desc), vm);  
  notify(vm);  
  
  if (vm.count("help")) {  
    cout << desc << "\n";  
    return 1;  
  }  
  
  if (vm.count("compression")) {  
    cout << "Compression level was set to " << vm["compression"].as<int>()  
         << ".\n";  
  } else {  
    cout << "Compression level was not set.\n";  
  }  
}  
~~~  
  
<sup>I'm using program_options so that I get obvious link errors on attempts to link against the wrong Boost (GCC+libstc++ / Clang+libc++).</sup>  
  
I'm using invocations like:  
  
~~~sh  
/some/specific/cmake -Bcmbi-clang -DBOOST_ROOT=/opt/obscure/boost_1_70_0_clang_c++14_build  -H. -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_C_COMPILER=/usr/bin/clang -DCMAKE_CXX_COMPILER=/usr/bin/clang++ -DCMAKE_CXX_FLAGS=-stdlib=libc++  
/some/specific/cmake -Bcmbi-gcc   -DBOOST_ROOT=/opt/obscure/boost_1_70_0_gcc_c++14_build    -H. -DCMAKE_BUILD_TYPE=RELEASE  
~~~  
  
I'm using `cmake` from the `Linux-x86_64.tar.gz` downloads on https://cmake.org/download. I'm running on Ubuntu 18.04.2.

---

## Comment 2

> Username: pdimov  
> Created at: 2019-07-04 15:59:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508527237  

`find_package` has two modes, "module mode", using FindBoost.cmake, and "config mode", using BoostConfig.cmake. The default is module mode, but FindBoost has logic to automatically find and use a config file. This is documented here: https://cmake.org/cmake/help/v3.15/module/FindBoost.html#boost-cmake  
  
If you want to disable the use of BoostConfig (installed by 1.70), set `Boost_NO_BOOST_CMAKE=ON`. If you want to keep using BoostConfig, but control the directory in which it's found, set `Boost_DIR`, which is the documented mechanism to control the search. See https://cmake.org/cmake/help/v3.15/command/find_package.html#search-procedure  
  
For this specific use case, you may consider building and installing Boost with `--layout=versioned`, in which case the `gcc` and `clang` libraries can go into a single directory; BoostConfig will select the correct libraries based on the CMake compiler id.

---

## Comment 3

> Username: tonyelewis  
> Created at: 2019-07-04 20:16:49 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508569236  

Thanks very much for your very quick and very helpful response.  
  
I think your explanation is much clearer than the ones in the documentation to which you linked - even after you've highlighted the relevant sections, I can barely make out the solution to my problem from it.  
  
I feel like many people are likely to hit this sort of issue and will, like me, waste a lot of time wondering what they've done wrong. Is there some way we could try to mitigate this? Could the config mode detect when `BOOST_ROOT` has been specified an print a helpful warning message?  
  
From my experiments, I think I'm finding that:  
* for `Boost_NO_BOOST_CMAKE`, it will be heeded if specified via a command-line flag of `-DBoost_NO_BOOST_CMAKE=ON` but ignored if via an environment variable  
* for `Boost_DIR`, it will be heeded if specified via an environment variable `Boost_DIR=/my/dir` but ignored if via command line flag  
  
Is that correct? Is it possible to make both work via both?  
  
Thanks very much for all your work on this really important area. It's much appreciated.

---

## Comment 4

> Username: tonyelewis  
> Created at: 2019-07-04 20:53:39 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508574993  

For reference (potentially for other people that may stumble across this issue), I'm finding these toolchain files (`-DCMAKE_TOOLCHAIN_FILE=file.cmake`) do the trick:  
  
`clang-168.toolchain.cmake` :  
  
~~~cmake  
set( Boost_NO_BOOST_CMAKE ON                                  )  
set( BOOST_ROOT           /opt/boost_1_68_0_clang_c++14_build )  
set( CMAKE_BUILD_TYPE     RELEASE                             )  
set( CMAKE_C_COMPILER     /usr/bin/clang                      )  
set( CMAKE_CXX_COMPILER   /usr/bin/clang++                    )  
set( CMAKE_CXX_FLAGS      -stdlib=libc++                      )  
~~~  
  
`clang-170.toolchain.cmake` :  
  
~~~cmake  
  
set( ENV{Boost_DIR}       /opt/boost_1_70_0_clang_c++14_build )  
set( CMAKE_BUILD_TYPE     RELEASE                             )  
set( CMAKE_C_COMPILER     /usr/bin/clang                      )  
set( CMAKE_CXX_COMPILER   /usr/bin/clang++                    )  
set( CMAKE_CXX_FLAGS      -stdlib=libc++                      )  
~~~  
  
`gcc-168.toolchain.cmake` :  
  
~~~cmake  
set( Boost_NO_BOOST_CMAKE ON                                  )  
set( BOOST_ROOT           /opt/boost_1_68_0_gcc_c++14_build   )  
set( CMAKE_BUILD_TYPE     RELEASE                             )  
~~~  
  
`gcc-170.toolchain.cmake` :  
  
~~~cmake  
  
set( ENV{Boost_DIR}       /opt/boost_1_70_0_gcc_c++14_build   )  
set( CMAKE_BUILD_TYPE     RELEASE                             )  
~~~

---

## Comment 5

> Username: pdimov  
> Created at: 2019-07-05 07:58:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508668865  

I think that `set(Boost_DIR "/my/dir")` needs `CACHE` in order to work.  
  
There is also the option of using `Boost_ROOT`, both as an environment variable and a CMake variable. The subtlety there is that pre-3.12, it's ignored for compatibility reasons. In other words, `cmake_minimum_required(3.7)` will turn it off. See https://cmake.org/cmake/help/v3.15/policy/CMP0074.html#policy:CMP0074  
  
This is all CMake behavior, we have no control over it.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-07-05 08:01:55 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508669841  

No control, that is, apart from filing issues against CMake. :-)  
  
https://gitlab.kitware.com/cmake/cmake/issues?scope=all&utf8=%E2%9C%93&state=opened&search=boost

---

## Comment 7

> Username: tonyelewis  
> Created at: 2019-07-05 08:45:56 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508683006  

So my current understanding is:  
* For Boost &leq; 1.69, we can specify the location with `BOOST_ROOT` as a command line parameter, `set()`, or environment variable.  
* The world is :heart: :tulip: :sun_with_face: :sunglasses: :thumbsup:  
* But from &geq; Boost 1.70, it will start preferring to use "config mode", meaning it will silently ignore the `BOOST_ROOT` if we specify it via the command line or `set()`.  
* But it will continue heeding it if we specify it via an environment variable.  
* But the new config mode will find Boost directories in new places (eg `/opt`) and these will silently override our `BOOST_ROOT`, even if specified via an environment variable (that it otherwise heeds).  
* To revert to the previous, non "config mode" behaviour, we must specify `Boost_NO_BOOST_CMAKE=ON`  
* But this must be specified via a command line parameter or `set()`; if we specify it via an environment variable, it will be silently ignored.  
* To specify the location under "config mode", we can specify `Boost_DIR`  
* But this must be specified as an environment variable or `set( Boost_DIR <value> CACHE )`; if it is specified via the command line or the a plain `set( Boost_DIR <value )`, it will be silently ignored.  
* There is further complexity with `Boost_ROOT` (the same as `BOOST_ROOT`?) and the version of cmake we're using and the version of cmake we've required.  
* The world is :broken_heart: :fire: :cloud_with_lightning_and_rain: :confused: :thumbsdown:  
  
Does that sound about right?  
  
To me, this story, even if only rougly accurate is a recipe for much confusion and frustration. I'm happy to raise issues against CMake if that might help but I'm unclear about what can be plausibly fixed.  In particular, what are issues in `FindBoost.cmake` rather than fundamental issues with the CMake architecture?

---

## Comment 8

> Username: pdimov  
> Created at: 2019-07-05 08:54:27 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508685648  

To confuse matters further, `BOOST_ROOT` is not the same as `Boost_ROOT`. The former is what `FindBoost.cmake` looks at; the latter is what CMake looks at (`<PACKAGE>_ROOT`, with `<PACKAGE>` being `Boost` in our case - the argument given to `find_package`).  
  
Since `FindBoost.cmake` was written before the CMake naming conventions were established, it deviates from them. It arguably should be changed to look at `Boost_ROOT`, if it doesn't already, but even so, we can't retroactively fix older CMake versions.

---

## Comment 9

> Username: pdimov  
> Created at: 2019-07-05 08:57:13 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-508686456  

I suppose that `FindBoost.cmake` can be changed to look for BoostConfig.cmake in `BOOST_ROOT`, when that is set, via something like `find_package(Boost CONFIG PATHS ${BOOST_ROOT})`. You could try filing a CMake issue to that effect.

---

## Comment 10

> Username: DenizThatMenace  
> Created at: 2019-10-31 12:19:23 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-548345299  

>     * To specify the location under "config mode", we can specify `Boost_DIR`  
>   
>     * But this must be specified as an environment variable or `set( Boost_DIR <value> CACHE )`; if it is specified via the command line or the a plain `set( Boost_DIR <value )`, it will be silently ignored.  
  
You could also add the path you would otherwise set in a `Boost_DIR` cache-variable to the list stored in the `CMAKE_PREFIX_PATH` variable (no need for a cache-variable) before trying to locate Boost in "config mode".  
  
The only side-effect is, that if you do not restore the former value of `CMAKE_PREFIX_PATH`afterwards other calls to `find_package` for other libraries might also consider that path (and might find non-desired versions in that path).  
  
Then again you could always use the `PATHS` or `HINTS`option of `find_package` to point to a search-location.

---

## Comment 11

> Username: jdmairs  
> Created at: 2019-12-28 21:22:34 UTC  
> Updated at: 2019-12-28 23:46:44 UTC  
> Url: https://github.com/boostorg/boost_install/issues/12#issuecomment-569452141  

Dang even with this great help after 4 hours I still cannot get my Centos7 gcc 8.2 static build of Boost 1.72 to be found from CMake 3.15.  
  
Arrrgghh!  
  
Update: I had to set via shell environment variable  
export Boost_DIR=/opt/boost/boost_1_72_0/stage/lib/cmake  
  
It also worked via the ENV trick in the toolchain file.
