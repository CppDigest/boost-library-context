# #742 - Latest intel compiler support (oneapi) [Closed]

> Username: steinmig  
> Created at: 2021-10-19 12:36:28 UTC  
> Updated at: 2021-10-19 13:12:49 UTC  
> Closed at: 2021-10-19 13:12:48 UTC  
> Url: https://github.com/boostorg/build/issues/742  

I tried to compile boost with the latest intel compiler release (oneapi/Intel-2021.4) both with icpx and icpc.  
  
1) The various `.jam` files in `libs` have various `-wd` compiler flags, which are not supported for the latest open-source intel compiler: [see here](https://www.intel.com/content/www/us/en/develop/documentation/oneapi-dpcpp-cpp-compiler-dev-guide-and-reference/top/compiler-reference/compiler-options/alphabetical-list-of-compiler-options.html)  
  
2) The flag for specifying the filename of the partially compiled header files (`-pch-create`) is also not supported anymore, but also the new flag `-Fp` gave me the error that the to-be-created pchi file does not exist? When I simply deleted the flag and `$(<)` from the two compile commands in `intel.jam` that included this flag, the pipeline ran through. So is this even necessary?

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-10-19 13:12:47 UTC  
> Url: https://github.com/boostorg/build/issues/742#issuecomment-946709630  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
