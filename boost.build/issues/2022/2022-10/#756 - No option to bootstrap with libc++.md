# #756 - No option to bootstrap with libc++ [Closed]

> Username: darkdragon-001  
> Created at: 2022-10-04 12:29:44 UTC  
> Updated at: 2022-10-04 13:47:01 UTC  
> Closed at: 2022-10-04 12:30:35 UTC  
> Url: https://github.com/boostorg/build/issues/756  

`bootstrap.sh` since https://github.com/boostorg/boost/pull/466 (clearing `CXX CXXFLAGS` variables) makes the proposed fix in https://github.com/boostorg/boost/issues/207#issuecomment-441851110 not possible any more: It fails already when building `b2` before starting to building `boost`.  
  
I suggest giving `bootstrap.sh` a possibility to pass additional options to `build.sh` via one of the following ways:  
  
    extra argument (e.g. `buildflags='--cxxflags="-stdlib=libc++"'`)  
    all unsupported options  
    all options after `--`  
  
Copied from #746 as `bootstrap.sh` is still in this repository and `b2` contains all necessary mechanism required but they are not made use of by `bootstrap.sh`.

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2022-10-04 12:30:34 UTC  
> Url: https://github.com/boostorg/build/issues/756#issuecomment-1266917590  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues

---

## Comment 2

> Username: grafikrobot  
> Created at: 2022-10-04 13:47:01 UTC  
> Url: https://github.com/boostorg/build/issues/756#issuecomment-1267028527  

You should repost this to here <https://github.com/boostorg/boost/issues>.
