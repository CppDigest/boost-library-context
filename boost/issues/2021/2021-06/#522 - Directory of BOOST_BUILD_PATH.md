# #522 - Directory of BOOST_BUILD_PATH [Closed]

> Username: RodrigoFBernardo  
> Created at: 2021-06-09 13:21:15 UTC  
> Updated at: 2021-06-15 08:51:46 UTC  
> Closed at: 2021-06-15 08:51:46 UTC  
> Url: https://github.com/boostorg/boost/issues/522  

I intend to work with owlcpp  and in the installation process I need to install Boost. I am working on the version of ubuntu 18.04.  
  
When I try to run the example for testing:  
  
b2 release  
  
I have this error:  
  
   'boost-build /usr/include/boost ;'  
  
but we were unable to find "bootstrap.jam" in the specified directory  
or in BOOST_BUILD_PATH (searching /usr/include/boost, /usr/share/boost-build).  
  
When I install or Boost generated header file is in /usr/local/lib/boost/include, and the binary library file is in /usr/local/lib/boost/lib  
  
I checked the installation and everything is fine.
