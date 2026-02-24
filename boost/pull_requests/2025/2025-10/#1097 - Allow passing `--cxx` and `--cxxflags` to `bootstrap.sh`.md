# #1097 Allow passing `--cxx` and `--cxxflags` to `bootstrap.sh` [Open]

> Username: Flamefire  
> Created at: 2025-10-30 13:20:47 UTC  
> Updated at: 2025-10-30 13:27:43 UTC  
> Url: https://github.com/boostorg/boost/pull/1097  

This allows selecting a specific compiler for building if e.g. `g++` is not available but only `g++-15`.  
  
As `$TOOLSET` isn't set in this case, unless passed by the user, that part of the project-config needs to be omitted. The alternative is to use "cxx" as what `build.sh` does but that might also not be what the user wanted.

---
