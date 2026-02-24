# #32 - Possible build configuration error? [Closed]

> Username: madmongo1  
> Created at: 2020-05-21 16:00:39 UTC  
> Updated at: 2020-05-21 16:25:04 UTC  
> Closed at: 2020-05-21 16:23:27 UTC  
> Url: https://github.com/boostorg/atomic/issues/32  

Steps to reproduce:  
  
1. Check out current develop of boost and update all submodules.  
2. Ensure clang is installed (I am using clang-10, but it's probably not important)  
3. Execute the following statements in a bash shell:  
```bash  
$ ./bootstrap.sh  
$ ./b2 toolset=clang variant=release,debug cxxstd=2a,17,14,11 -j`grep processor /proc/cpuinfo | wc -l` test  
```  
  
Observed Results:  
  
... time passes...  
  
```  
error: Name clash for '<p/devel/boost/stage/lib>libboost_atomic.so.1.74.0'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  <cxxstd>2a  
error:     -  <cxxstd>17  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```  
  
Expected Results:  
  
Completion of configuration, compilation and tests passing.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-05-21 16:23:27 UTC  
> Updated at: 2020-05-21 16:25:04 UTC  
> Url: https://github.com/boostorg/atomic/issues/32#issuecomment-632188824  

I don't know what the `test` target means. The documented superproject targets are `install` and `stage`.  
  
However, my understanding is that the `test` target involves `stage` at some point. Since you're building multiple versions of the libraries for different `cxxstd` values, and the resulting file names are not mangled depending on this feature, you get the error about the library file name clash in the stage directory. I don't think you can build `stage` or `install` targets for multiple versions of libraries in one `b2` command because of this file name clash.  
  
You can build each version of the libraries in separate `b2` runs by specifying different stage directories in the `--stagedir=<dir>` command line parameter.  
  
If you really just want to run tests of some library then you can run `b2` with the `libs/<libname>/test` as the target name (or just running `b2` in the `libs/<libname>/test` directory with no target). Since doing so does not involve `stage` or `install`, you can specify multiple `cxxstd` values in one run (the built binaries will not be installed in the stage directory, but instead will be used from the build tree, which does have separate directories for different property values).  
  
This is not specific to Boost.Atomic and not something that can be changed on Boost.Atomic side. This is just how Boost.Build operates.
