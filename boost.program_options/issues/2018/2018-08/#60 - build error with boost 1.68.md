# #60 - build error with boost 1.68 [Open]

> Username: chuet-thermo  
> Created at: 2018-08-20 14:22:50 UTC  
> Updated at: 2026-02-19 20:04:26 UTC  
> Url: https://github.com/boostorg/program_options/issues/60  

When building boost 1.68, I have an error when building program_options where it cannot copy the program_options-mt.lib file.  
  
It seems the .lib file is not created since nothing is exported when not using `BOOST_ALL_DYN_LIB`.  
  
Taking example on boost.timer, I modified the Jamfile.v2 file to look like this   
```  
boost-lib program_options  
    : $(SOURCES).cpp  
    : # See https://svn.boost.org/trac/boost/ticket/5049  
      <target-os>hpux,<toolset>gcc:<define>_INCLUDE_STDC__SOURCE_199901  
      <link>shared:<define>BOOST_PROGRAM_OPTIONS_DYN_LINK=1  
      <link>static:<define>BOOST_PROGRAM_OPTIONS_STATIC_LINK=1  
    ;  
```  
  
and it seems to fix my issue.

---

## Comment 1

> Username: vprus  
> Created at: 2018-08-22 06:58:49 UTC  
> Url: https://github.com/boostorg/program_options/issues/60#issuecomment-414931724  

Thanks for the report!  
  
I don't think that program options ever defined BOOST_PROGRAM_OPTIONS_STATIC_LINK, do you know if that's a new change in Boost.Config? What compiler and platform are you using?

---

## Comment 2

> Username: chuet-thermo  
> Created at: 2018-08-22 07:04:03 UTC  
> Url: https://github.com/boostorg/program_options/issues/60#issuecomment-414932911  

I added the `BOOST_PROGRAM_OPTIONS_STATIC_LINK` define as I copy/pasted from boost.timer, without regard for its usefulness.  
  
I have no idea if it is necessary or not.

---

## Comment 3

> Username: vprus  
> Created at: 2018-08-22 07:05:12 UTC  
> Url: https://github.com/boostorg/program_options/issues/60#issuecomment-414933151  

And, what's your compiler and platform?

---

## Comment 4

> Username: chuet-thermo  
> Created at: 2018-08-22 07:08:08 UTC  
> Url: https://github.com/boostorg/program_options/issues/60#issuecomment-414933818  

sorry, Windows 7 MSVC 12 2013 and MSVC 14 2015, both in x64.  
  
I think I tried the x86 as well with the same results but am not sure.
