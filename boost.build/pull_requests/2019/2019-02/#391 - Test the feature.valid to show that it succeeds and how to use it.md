# #391 Test the feature.valid to show that it succeeds and how to use it. [Merged]

> Username: tee3  
> Created at: 2019-02-06 17:51:43 UTC  
> Updated at: 2021-10-02 21:19:02 UTC  
> Merged at: 2019-02-23 15:24:49 UTC  
> Closed at: 2019-02-23 15:24:50 UTC  
> Url: https://github.com/boostorg/build/pull/391  

This pull request just adds some tests for the `feature.valid` rule.  I added this as I had made a mistake in using the rule in some of my own code and thought I had found a bug.  This test will also show users how to use this rule (if they read the source).  
  
I was using this rule to adding a workaround for `<cxxstd>` in older versions of Boost.Build, such as exist in Debian 9 versions of Boost.Build.  
  
The workaround is terrible and looks something like the following.  There's almost certainly a cleaner way to do this, but I'm just using it as an example for using `feature.valid` for this pull request.  
  
```  
# cxxstd-feature-workaround.jam  
import feature ;  
  
cxxstd-requirements = ;  
  
if ! [ feature.valid <cxxstd> ]  
{  
  feature.feature cxxstd : 98 11 14 17 2a : optional composite propagated ;  
  
  cxxstd-requirements =  
    "<cxxstd>98,<toolset>darwin:<cxxflags>-std=c++03"  
    "<cxxstd>98,<toolset>gcc:<cxxflags>-std=c++03"  
    "<cxxstd>98,<toolset>clang:<cxxflags>-std=c++03"  
    "<cxxstd>11,<toolset>darwin:<cxxflags>-std=c++11"  
    "<cxxstd>11,<toolset>gcc:<cxxflags>-std=c++11"  
    "<cxxstd>11,<toolset>clang:<cxxflags>-std=c++11"  
    "<cxxstd>14,<toolset>msvc:<cxxflags>/std:c++14"  
    "<cxxstd>14,<toolset>darwin:<cxxflags>-std=c++14"  
    "<cxxstd>14,<toolset>gcc:<cxxflags>-std=c++14"  
    "<cxxstd>14,<toolset>clang:<cxxflags>-std=c++14"  
    "<cxxstd>17,<toolset>msvc:<cxxflags>/std:c++17"  
    "<cxxstd>17,<toolset>darwin:<cxxflags>-std=c++17"  
    "<cxxstd>17,<toolset>gcc:<cxxflags>-std=c++17"  
    "<cxxstd>17,<toolset>clang:<cxxflags>-std=c++17"  
    "<cxxstd>2a,<toolset>msvc:<cxxflags>/std:latest"  
    "<cxxstd>2a,<toolset>darwin:<cxxflags>-std=c++2a"  
    "<cxxstd>2a,<toolset>gcc:<cxxflags>-std=c++2a"  
    "<cxxstd>2a,<toolset>clang:<cxxflags>-std=c++2a"  
    ;  
}  
```  
  
```  
import cxxstd-feature-workaround ;  
  
project : requirements $(cxxstd-requirements) ;  
```

---
