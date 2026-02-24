# #6 [CMake] Run cmake from CI [Merged]

> Username: Mike-Devel  
> Created at: 2019-05-07 20:41:49 UTC  
> Updated at: 2019-05-08 14:42:00 UTC  
> Merged at: 2019-05-08 13:59:09 UTC  
> Closed at: 2019-05-08 13:59:09 UTC  
> Url: https://github.com/boostorg/ratio/pull/6  

This is my initial draft for running the cmake script on the CI servers.  
The appveyor seems to be broken for reasons unrelated to cmake.  
  
Explanation:  
The file `test/test_cmake/CMakeLists.txt` will process all checked out libraries with a cmake file including ratio (in the ci scripts only the libraries that ratio depends on are checked out) just as the user would do. It ss essentiall a copy of the one proposed here: https://github.com/boostorg/boost/pull/193/files and once something like that is added to the superproject, this local copy can be removed.

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2019-05-08 07:41:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ratio/pull/6#pullrequestreview-234908246  

📁 test/test_cmake/CMakeLists.txt

```diff
  16 |+ #
  17 |+ # Those libraries can be excluded here
  18 |+ set(BOOST_MAIN_IGNORE_LIBS callable_traits;hof;compute;gil;hana;yap;safe_numerics;beast CACHE STRING "List of libraries that will be excluded from cmake build")
```

> Username: viboes  
> Created_at: 2019-05-08 07:41:55 UTC  
> Updated_at: 2019-05-08 11:26:37 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r281948741  

Why do you need this?

> Username: Mike-Devel  
> Created_at: 2019-05-08 08:22:16 UTC  
> Updated_at: 2019-05-08 11:26:37 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r281961916  

It is not necessary for ci, where you anyway only checkout your dependencies. It might be useful however, if you test it on your local machine in the context of a full boost checkout.

> Username: viboes  
> Created_at: 2019-05-08 13:55:26 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r282077471  

My question was, why do we need to ignore the explicitly. Why would these libraries be concerned. It is the algorithm that find them as dependencies?


---

## Review 2 [Commented]

> Username: viboes  
> Created_at: 2019-05-08 07:45:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ratio/pull/6#pullrequestreview-234909858  

📁 .travis.yml

```diff
 314 |     echo "using $TOOLSET : : $COMPILER : <cxxflags>-std=$CXXSTD ;" > ~/user-config.jam
 315 |   - ./b2 libs/ratio/test toolset=$TOOLSET
 316 |+   - mkdir __wsl__ && cd __wsl__
```

> Username: viboes  
> Created_at: 2019-05-08 07:45:55 UTC  
> Updated_at: 2019-05-08 11:26:37 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r281950008  

Just for curiosity, what is this __wsl__? What it stands for?

> Username: Mike-Devel  
> Created_at: 2019-05-08 08:18:02 UTC  
> Updated_at: 2019-05-08 11:26:37 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r281960477  

Stupid me - that should be `__build__ `  
(the name doesn't matter,  but I was testing this with the windows subsystem for linux) will fix.

> Username: viboes  
> Created_at: 2019-05-08 13:58:21 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r282078803  

Why do you prefix and suffix build with __?


---

## Review 3 [Commented]

> Username: viboes  
> Created_at: 2019-05-08 08:08:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/ratio/pull/6#pullrequestreview-234919166  

> Username: viboes  
> Created_at: 2019-05-08 08:08:37 UTC  
> Updated_at: 2019-05-08 11:26:37 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r281957388  

I don't know what is going on with appveyor but it was not finding already the compile rule for the jamfile  
  
ERROR: rule "compile" unknown in module "Jamfile<C:\projects\boost-root\libs\ratio\test>".  
  
This is weird.

> Username: Mike-Devel  
> Created_at: 2019-05-08 11:30:12 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r282024982  

from the commit history it seems that the appveyor script was broken since the ci script was originally added.  
I'm unfortunately not a b2 nor appveyor expert, otherwise I'd have tried to fix it on the go.

> Username: viboes  
> Created_at: 2019-05-08 13:52:44 UTC  
> Url: https://github.com/boostorg/ratio/pull/6#discussion_r282076279  

I did the diff with the Boost.Chrono file and I don't see any difference. I don't see what is the problem


---
