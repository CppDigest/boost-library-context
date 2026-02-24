# #17 - Add option to auto-use --jobs [Open]

> Username: Flamefire  
> Created at: 2021-10-31 11:52:02 UTC  
> Updated at: 2021-10-31 11:52:02 UTC  
> Url: https://github.com/boostorg/boostdep/issues/17  

Git 2.8.0 added the `--jobs` param to fetch submodules in parallel. E.g. `git submodule -q update --init --jobs 3 libs/config libs/headers libs/../tools/boost_install libs/../tools/build libs/../tools/cmake`  
  
Would be great if boostdep had an option to auto-use that when possible. Checking the git version in Bash is a bit cumbersome and using `--git_args "--jobs 3"` is leading to errors for older git versions
