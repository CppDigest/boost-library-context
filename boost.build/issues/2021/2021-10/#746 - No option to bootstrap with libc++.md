# #746 - No option to bootstrap with libc++ [Closed]

> Username: darkdragon-001  
> Created at: 2021-10-19 13:29:35 UTC  
> Updated at: 2021-11-13 14:53:06 UTC  
> Closed at: 2021-11-13 14:53:05 UTC  
> Url: https://github.com/boostorg/build/issues/746  

`bootstrap.sh` since boostorg/boost#466 (clearing `CXX CXXFLAGS` variables) makes the proposed fix in https://github.com/boostorg/boost/issues/207#issuecomment-441851110 not possible any more: It fails already when building `b2` _before_ starting to building `boost`.  
  
I suggest giving `bootstrap.sh` a possibility to pass additional options to `build.sh` via one of the following ways:  
1. extra argument (e.g. `buildflags='--cxxflags="-stdlib=libc++"'`)  
2. all unsupported options  
3. all options after `--`

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-11-13 14:53:04 UTC  
> Url: https://github.com/boostorg/build/issues/746#issuecomment-968080664  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
