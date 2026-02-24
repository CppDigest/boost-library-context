# #326 - Config_link_test strangeness [Closed]

> Username: eldiener  
> Created at: 2020-03-21 18:35:27 UTC  
> Updated at: 2020-04-08 14:46:48 UTC  
> Closed at: 2020-04-08 14:46:48 UTC  
> Url: https://github.com/boostorg/config/issues/326  

In the config_link_test main.cpp is evidently being compiled as multi-threaded despite the jamfile line of:  
  
run main.cpp link_test : : : <define>BOOST_DYN_LINK=1 <define>BOOST_CONFIG_NO_LIB <link>shared <runtime-link>shared <threading>single : link_test_test ;  
  
I can see that because the output is being placed in a threading-multi subdirectory and the toolsets I am testing have their multi-threading option turned on in the command line. Meanwhile link_test.cpp is being compiled as single threaded. I can also see that for the same reasons as above.  
  
Of course this could be something in my setup but I know I am not passing threading multi ot single on my command line. Could you please check this out. I am testing on the latest 'develop' branch for 'config' and 'build'.  
  
I found this out because because I am testing an Embarcadero toolset for the latest clang-based C++ compilers from Embarcadero, which has been pushed to build's 'develop' branch ( it changed nothing in build other than the borland.jam toolset which I updated and the new embarcadero.jam toolset ) and the problem above occurred when I was trying to test my config changes for the Embarcadero clang-based bcc64 compiler with the toolset. I quickly checked out config 'develop' and tried the same config tests with the latest gcc-9.2 compiler and saw the same problem I am reporting here. Perhaps something in build's 'develop' branch is causing the above. But clearly if the main routine is multi-threaded and the library is single-threaded the test is probably not going to give the correct result.

---

## Comment 1

> Username: eldiener  
> Created at: 2020-03-21 20:27:39 UTC  
> Url: https://github.com/boostorg/config/issues/326#issuecomment-602100011  

I am seeing the same thing using Boost Build from the 1.72 distribution.

---

## Comment 2

> Username: eldiener  
> Created at: 2020-03-22 19:52:36 UTC  
> Url: https://github.com/boostorg/config/issues/326#issuecomment-602262936  

A fix for this issue is a small part of the PR https://github.com/boostorg/config/pull/327.
