# #510 - Need help installing boost-1.69.0 on osX 11.2.1 BigSur [Open]

> Username: harshakokel  
> Created at: 2021-04-29 08:17:55 UTC  
> Updated at: 2021-04-29 08:17:55 UTC  
> Url: https://github.com/boostorg/boost/issues/510  

Hello,  
  
I find myself needing boost-1.69.0 for a project and I am trying to follow following steps to get that version.  
  
1. Copy the [1.69.0](https://www.boost.org/users/history/version_1_69_0.html) tar.gz to brew cache  
    - `mv boost-1.69.0.tar.bz2 $(brew --cache -s boost)`  
2. Revert home brew [formula](https://github.com/Homebrew/homebrew-core/blob/b4de2e1ce084ec249ca61c3eff6f96617e38c081/Formula/boost.rb) to 1.69.0  
    - open the formula by `brew edit boost`  
    - copy paste the 1.69.0 formula  
2. Install boost from source  
    - `brew install --build-from-source boost`  
  
However, I am facing the following error and the boost is not installed successfully.  
  
```  
==> ./bootstrap.sh --prefix=/usr/local/Cellar/boost/1.69.0 --libdir=/usr/local/Cellar/boost/1.69.0/lib --with-icu=/usr/local/opt/icu4c --without-librar  
==> ./b2 headers  
==> ./b2 --prefix=/usr/local/Cellar/boost/1.69.0 --libdir=/usr/local/Cellar/boost/1.69.0/lib -d2 -j12 --layout=tagged-1.66 --user-config=user-config.ja  
Last 15 lines from /Users/****/Library/Logs/Homebrew/boost/03.b2:  
1 warning generated.  
...skipped <pbin.v2/libs/container/build/darwin-12.0.0/release/visibility-hidden>libboost_container.dylib for lack of <pbin.v2/libs/container/build/darwin-12.0.0/release/visibility-hidden>dlmalloc.o...  
...skipped <p/usr/local/Cellar/boost/1.69.0/lib>libboost_container.dylib for lack of <pbin.v2/libs/container/build/darwin-12.0.0/release/visibility-hidden>libboost_container.dylib...  
darwin.compile.c bin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden/alloc_lib.o  
  
    "clang++" -x c -O3 -Wall -fvisibility=hidden -gdwarf-2 -fexceptions -Wno-long-double -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTAINER_STATIC_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden/alloc_lib.o" "libs/container/src/alloc_lib.c"  
  
warning: unknown warning option '-Wno-long-double'; did you mean '-Wno-long-long'? [-Wunknown-warning-option]  
1 warning generated.  
...skipped <pbin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden>libboost_container.a(clean) for lack of <pbin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden>dlmalloc.o...  
...skipped <pbin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden>libboost_container.a for lack of <pbin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden>dlmalloc.o...  
...skipped <p/usr/local/Cellar/boost/1.69.0/lib>libboost_container.a for lack of <pbin.v2/libs/container/build/darwin-12.0.0/release/link-static/visibility-hidden>libboost_container.a...  
...failed updating 726 targets...  
...skipped 628 targets...  
...updated 15018 targets...  
```  
  
Can someone please guide me on what can I do to resolve this? Or if there is any better way to install 1.69.0?   
  
I also tried building from source as follows:  
* Unpack and go into the directory: `tar -xzf boost_1_690_0.tar.gz`  
* `cd boost_1_69_0`  
* Configure: `./bootstrap.sh`  
* Build: `./b2`  
* Install: `./b2 install`  
  
But I faced `clang: error: unknown argument: '-fcoalesce-templates'`.   
  
Any help would be greatly appreciated. Thanks in advance.
