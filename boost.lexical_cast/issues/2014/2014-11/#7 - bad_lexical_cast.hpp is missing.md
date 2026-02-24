# #7 - bad_lexical_cast.hpp is missing [Closed]

> Username: oblitum  
> Created at: 2014-11-18 01:42:55 UTC  
> Updated at: 2014-11-28 03:48:44 UTC  
> Closed at: 2014-11-28 03:48:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/7  

In my system, I've installed boost in `/opt/local` from [git tag 1.57](https://github.com/boostorg/boost/releases/tag/boost-1.57.0). Formally there wasn't any `/opt/local/include/boost` there, it was clean.  
  
Given the following building/installation procedure  
  
```  
./bootstrap.sh --prefix=/opt/local --with-toolset=clang --with-icu=/usr/local/opt/icu4c  
  
./b2 -q -j 4 --prefix="/opt/local" --build-dir="/opt/src/.build" toolset=clang variant=release optimization=speed cxxflags="-std=c++11 -stdlib=libc++" linkflags="-stdlib=libc++" install  
```  
  
`lexical_cast.hpp` is copied to `/opt/local/include/boost/lexical_cast.hpp`, but there isn't a `/opt/local/include/boost/lexical_cast` directory containing `bad_lexical_cast.hpp`, etc. It's in the source directory, but it was not copied on installation.  
  
I believe it's because of some recent header spliting refactoring.

---

## Comment 1

> Username: apolukhin  
> Created at: 2014-11-18 06:09:48 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/7#issuecomment-63427077  

There've been some changes to the build system. Try adding `./b2 headers`:  
  
```  
./bootstrap.sh --prefix=/opt/local --with-toolset=clang --with-icu=/usr/local/opt/icu4c  
./b2 headers  
./b2 -q -j 4 --prefix="/opt/local" --build-dir="/opt/src/.build" toolset=clang variant=release optimization=speed cxxflags="-std=c++11 -stdlib=libc++" linkflags="-stdlib=libc++" install  
```  
  
If it helps (or if not), tell me please.

---

## Comment 2

> Username: oblitum  
> Created at: 2014-11-28 03:46:02 UTC  
> Updated at: 2014-11-28 03:46:11 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/7#issuecomment-64850995  

@apolukhin Yes, that copies the `lexical_cast` directory to installation.

---

## Comment 3

> Username: oblitum  
> Created at: 2014-11-28 03:48:44 UTC  
> Url: https://github.com/boostorg/lexical_cast/issues/7#issuecomment-64851155  

@apolukhin sorry, that [was instructed in wiki](https://svn.boost.org/trac/boost/wiki/TryModBoost), my bad.
