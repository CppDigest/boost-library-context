# #262 - Building against multiple python implementations broken on 1.70 [Closed]

> Username: SoapGentoo  
> Created at: 2019-04-14 22:10:40 UTC  
> Updated at: 2019-10-08 21:05:25 UTC  
> Closed at: 2019-04-17 21:24:14 UTC  
> Url: https://github.com/boostorg/python/issues/262  

Hi @stefanseefeld   
Thanks for making Boost.Python usable with multiple Python 3 installations, this has massively simplified packaging for us downstream. On 1.70.0 we have new issues.  
  
We build boost like this:  
```  
b2 --user-config=/var/tmp/portage/dev-libs/boost-1.70.0/work/boost_1_70_0-abi_x86_64.amd64/user-config.jam python=2.7 python=3.6 python=3.7   
gentoorelease -j4 -q -d+2 pch=off -sICU_PATH=/usr --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boos  
t-1.70.0/image/usr --layout=system threading=multi link=shared -sNO_BZIP2=0 -sNO_LZMA=0 -sNO_ZLIB=0 -sNO_ZSTD=1  
```  
and then install it using  
```  
b2 --user-config=/var/tmp/portage/dev-libs/boost-1.70.0/work/boost_1_70_0-abi_x86_64.amd64/user-config.jam python=2.7 python=3.6 python=3.7   
gentoorelease -j4 -q -d+2 pch=off -sICU_PATH=/usr --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boos  
t-1.70.0/image/usr --layout=system threading=multi link=shared -sNO_BZIP2=0 -sNO_LZMA=0 -sNO_ZLIB=0 -sNO_ZSTD=1 --includedir=/var/tmp/portag  
e/dev-libs/boost-1.70.0/image/usr/include --libdir=/var/tmp/portage/dev-libs/boost-1.70.0/image/usr/lib64 install  
```  
which errors out with a message  
```  
error: Name clash for '<p/var/tmp/portage/dev-libs/boost-1.70.0/image/usr/lib64/cmake/boost_python-1.70.0>libboost_python-variant.cmake-shared.cmake'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <python>2.7  
error:     -  <python>3.6  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
The same command-line used to work fine with 1.69.0. Paging in @pdimov as he seemed to have been involved with changing the install targets between 1.69.0 and 1.70.0.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-14 23:50:55 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-483069294  

I'll need to look into this. Apparently, `FindBoost.cmake` solves this by forcing the user to specify the version in `find_package(Boost COMPONENTS python27)`, but the CMake configuration files installed by 1.70 only take `find_package(Boost COMPONENTS python)`, so multiple versions installed at the same time are a problem.  
  
In the meantime, you can add `--no-cmake-config` to your `b2 install` command line to skip the installation of CMake configuration files, which ought to give you the 1.69 behavior back.

---

## Comment 2

> Username: SoapGentoo  
> Created at: 2019-04-15 07:55:33 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-483147008  

@pdimov Thanks, that helped us build 1.70 without my ugly hack. For the time being, being able to produce N concurrent python installations is more important than the cmake config files produced by bjam.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-04-17 21:24:14 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-484267128  

Installing for multiple python versions has been fixed on the `develop` branch.

---

## Comment 4

> Username: henryiii  
> Created at: 2019-10-08 15:53:48 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-539580164  

In Boost 1.71, the Python config files produced have the same name, breaking homebrew packages if the config files are produced (See <https://github.com/Homebrew/homebrew-core/pull/44984> and matching issue). Is this fixed as well, post 1.71, or is it still a problem?  
  
For context, the homebrew boost build currently builds boost, boost-python, and boost-python3 in separate recipes, then they can be all available through symlinks into one system at the same time as long as they have no conflicting files. Currently, for the `-python*` parts, the files are copied in from the config directory. I modified it to install to a staging folder, then copy from there, in order to produce and capture the CMake config files, but now the two recipes conflict.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-10-08 16:06:20 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-539585704  

`boost_python-config.cmake` and `boost_python-config-version.cmake` are common for all Python versions, yes. In the same way they are common for all other build variants - debug/release, static/shared, 32/64 bit, single/multithreaded, and so on.  
  
I suppose you will have to extract them into a common package that's a dependency of both `boost-python` and `boost-python3`.

---

## Comment 6

> Username: henryiii  
> Created at: 2019-10-08 19:31:44 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-539667011  

They don't have any hard-coded info about the current build, then?

---

## Comment 7

> Username: pdimov  
> Created at: 2019-10-08 21:05:25 UTC  
> Url: https://github.com/boostorg/python/issues/262#issuecomment-539704076  

No; the information about the build variants is in separate `.cmake` files that are included by the main config. Your two recipes should produce identical `boost_python-config.cmake` and `boost_python-config-version.cmake` files and different `libboost_python-variant-*.cmake` files.
