# #743 - Faulty toolset specification in bootstrap.sh / build.sh [Closed]

> Username: steinmig  
> Created at: 2021-10-19 12:31:17 UTC  
> Updated at: 2021-10-19 13:13:21 UTC  
> Closed at: 2021-10-19 13:13:20 UTC  
> Url: https://github.com/boostorg/build/issues/743  

I tried to compile boost with the latest intel compiler release (oneapi/Intel-2021.4) both with icpx and icpc.  
  
I spotted 3 main issues:  
  
1) Specifying any intel toolset version is currently faulty due to the mechanism in `build.sh`. Here, in the function `check_toolset`, the toolset string is split based on `-` and in the function `test_toolset` only the first part is then compared against the input. Meaning that the function compares `intel` with its input, which is always `intel-linux` or `linux-darwin`. The function then runs through and guesses the toolset.  
  
2) not specifying a toolset correctly identifies in my case the `intel-linux` within the `tools/build/src/engine/build.sh` script if I have the intel oneapi at the default location (`/opt/intel/.....`), but the correctly identified toolset is not returned to `bootstrap.sh`, because the `$TOOLSET` variable is not altered, but kept empty. This leads to a faulty `project-config.jam` file:  
```jam  
if !  in [ feature.values <toolset> ]  
{  
    using  ;     
}  
  
project : default-build <toolset> ;  
```  
I get the same file with the procedure in 1), because the TOOLSET variable is set empty by build.sh?  
  
3) For the bootstrap/build pipeline the `CXXFLAGS` env variable is currently ignored. `build.sh` supports it as a command line argument, but does not check the env variable and bootstrap does not give the env variable to the build script as an argument. The CXXFLAGS should be supported for some "non-default" location setups.  
  
All of this is easy to change, but I am confused that there are so many dangling variables, so I am not sure whether I understood this correctly as written here (but some quick fixes were able to fix the pipeline as expected)

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2021-10-19 13:13:19 UTC  
> Url: https://github.com/boostorg/build/issues/743#issuecomment-946710130  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
