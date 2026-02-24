# #100 - MSVC warning when using iostreams as a dynamic lib [Open]

> Username: Vultraz  
> Created at: 2019-08-04 07:18:20 UTC  
> Updated at: 2019-11-20 02:37:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/100  

I have Iostreams 1.70 build using vcpkg as a dynamic lib, and when I use it in our project this warning shows up:  
  
```  
Warning	C4275	 non dll-interface class 'std::ios_base::failure' used as base for dll-interface class 'boost::iostreams::bzip2_error'	wesnoth	C:\Users\\Documents\vcpkg\installed\x64-windows\include\boost\iostreams\filter\bzip2.hpp	112	  
```
