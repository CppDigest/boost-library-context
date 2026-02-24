# #376 - b2 layout command line parameter is not docummented [Closed]

> Username: SSE4  
> Created at: 2018-12-13 15:43:20 UTC  
> Updated at: 2019-02-27 03:18:53 UTC  
> Closed at: 2019-02-27 03:18:52 UTC  
> Url: https://github.com/boostorg/build/issues/376  

I wasn't able to find documentation for b2 layout command line parameter.  
it seems like it's not even mentioned anywhere (https://www.google.com/search?q=site%3Ahttps%3A%2F%2Fboostorg.github.io%2Fbuild%2F+layout&ie=utf-8&oe=utf-8)  
  
the following page https://www.boost.org/doc/libs/1_69_0/more/getting_started/windows.html seems to be having lots of broken links (404), for instance:  
  
- For a complete description of these and other invocation options, please see the [Boost.Build documentation](https://boostorg.github.io/build/doc/html/bbv2/overview/invocation.html).  
- an up-to-date list is always available in the [Boost.Build documentation](https://boostorg.github.io/build/doc/html/bbv2/reference/tools.html)  
- If you previously chose a toolset for the purposes of [building b2](https://www.boost.org/doc/libs/1_69_0/doc/html/bbv2.html#bbv2.installation), you should assume it won't work and instead choose newly from the table below.  
  
this is very inconvenient. the only option to get some documentation, is to use `b2 --help`:  
```  
 --layout=<layout>       Determine whether to choose library names and header  
                          locations such that multiple versions of Boost or  
                          multiple compilers can be used on the same system.  
  
                              -- versioned -- Names of boost binaries include  
                              the Boost version number, name and version of  
                              the compiler and encoded build properties. Boost  
                              headers are installed in a subdirectory of  
                              <HDRDIR> whose name contains the Boost version  
                              number.  
  
                              -- tagged -- Names of boost binaries include the  
                              encoded build properties such as variant and  
                              threading, but do not including compiler name  
                              and version, or Boost version. This option is  
                              useful if you build several variants of Boost,  
                              using the same compiler.  
  
                              -- system -- Binaries names do not include the  
                              Boost version number or the name and version  
                              number of the compiler. Boost headers are  
                              installed directly into <HDRDIR>. This option is  
                              intended for system integrators building  
                              distribution packages.  
  
```  
however, this obviously requires to have b2 executable (which might be missing, e.g. if you're on Mobile, or using boost from package manager, or whatever).  
  
also, this documentation from `b2 --help` is very brief, as it doesn't fully describe on how final libraries/binaries will be named, there is no naming template, no examples, no specification on how "encoded build properties" are actually encoded, etc.  
  
/cc @apolukhin @grafikrobot

---

## Comment 1

> Username: grafikrobot  
> Created at: 2019-02-27 03:18:52 UTC  
> Url: https://github.com/boostorg/build/issues/376#issuecomment-467708015  

This is not a B2 option, it's a Boost C++ Libraries custom option. As such it would not work to add it to the B2 documentation as the option would not exist for users of B2 outside of Boost. Hence it should be documented in the Boost build & install documentation. Please open a ticket on the Boost issues page (https://github.com/boostorg/boost/issues). Same goes for the broken links in the Boost docs.
