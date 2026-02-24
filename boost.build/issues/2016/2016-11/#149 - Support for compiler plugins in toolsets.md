# #149 - Support for compiler plugins in toolsets [Open]

> Username: oakad  
> Created at: 2016-11-20 05:08:33 UTC  
> Updated at: 2016-11-20 05:08:33 UTC  
> Url: https://github.com/boostorg/build/issues/149  

Compiler plugins are getting in fashion, so it will be nice to have a support for them in boost-build.  
  
Assuming gcc toolset (clang is similar):  
  
1. Compiler plugin is a variety of shared library which is build with some options derived by running the target compiler (e.g. "gcc -print-file-name=plugin" to set the proper include path).  
  
2. As a dependency, compiler plugin is to be applied to the <cflags> for each affected source ("-fplugin=<path>/plugin.so").  
  
3. An extra feature will be useful to set plugin specific flags on affected sources ("-fplugin-arg-plugin-foo=bar"). A bit of complication is caused by the fact that any "-fplugin" flags must precede "-fplugin-arg-*" flags on the gcc command line.  
  
I have tried to work out a module of my own - p1 and p3 are pretty easy, but I could not get the p2 right (the whole generators/v-targets thing is pretty confusing),
