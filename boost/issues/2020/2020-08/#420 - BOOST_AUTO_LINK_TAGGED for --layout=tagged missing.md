# #420 - BOOST_AUTO_LINK_TAGGED for --layout=tagged missing [Open]

> Username: mahaase  
> Created at: 2020-08-12 11:00:55 UTC  
> Updated at: 2020-08-12 11:30:51 UTC  
> Url: https://github.com/boostorg/boost/issues/420  

If I build boost with `--layout=tagged` the flag `BOOST_AUTO_LINK_TAGGED` will not be set.  
  
e.g.: `libboost_chrono-variant-mt-x64-shared.cmake`  
```  
set_target_properties(Boost::chrono PROPERTIES  
  INTERFACE_COMPILE_DEFINITIONS "BOOST_CHRONO_DYN_LINK"  
  )  
```  
  
Information from documentation:  
```  
BOOST_AUTO_LINK_NOMANGLE: Specifies that we should link to BOOST_LIB_NAME.lib,  
                          rather than a mangled-name version.  
BOOST_AUTO_LINK_TAGGED:   Specifies that we link to libraries built with the --layout=tagged option.  
                          This is essentially the same as the default name-mangled version, but without  
                          the compiler name and version, or the Boost version.  Just the build options.  
BOOST_AUTO_LINK_SYSTEM:   Specifies that we link to libraries built with the --layout=system option.  
                          This is essentially the same as the non-name-mangled version, but with  
                          the prefix to differentiate static and dll builds  
```  
  
So currently I have to set `BOOST_AUTO_LINK_TAGGED` in `CXXFLAGS` for each module, which uses boost library.  
  
EDIT: should that be fixed in boostorg / cmake repository?
