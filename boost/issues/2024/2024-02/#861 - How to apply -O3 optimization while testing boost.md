# #861 - How to apply -O3 optimization while testing boost? [Open]

> Username: Ashish-Gautam-22  
> Created at: 2024-02-15 09:41:27 UTC  
> Updated at: 2024-02-15 09:41:27 UTC  
> Url: https://github.com/boostorg/boost/issues/861  

I am currently working on verifying and testing boost for the QNX platform. For some reason, there are some issues with the powl function on the platform which causes a couple tests in Random package to fail. After some investigation we found using the -O3 flag fixes it in our test exes. But I am unable to apply the same to the build using "b2.exe".   
  
The command that gets finally used to build is   
b2 --hash link=static toolset=qcc variant=release target-os=qnx address-model=64 architecture=x86 abi=sysv binary-format=elf linkflags="-V8.3.0,gcc_ntox86_64_gpp" cxxflags="-Wc,-std=c++14 -Wc,-std=gnu++14 -Wc,-fstack-protector-strong -Wc,-ftemplate-depth=1024 -Wc,-march=core2 -Wc,-mfpmath=sse -Wc,-fno-rounding-math -Wc,-msse2 -Wc,-fno-builtin -Wc,-w -Wc,-O3 -V8.3.0,gcc_ntox86_64_gpp" linkflags="-lstdc++ -lsocket" testing.launcher=ax_rexec --without-python --without-mpi define=BOOST_NO_CXX11_STD_ALIGN define=BOOST_NO_CXX11_HDR_CODECVT define="__GXX_EXPERIMENTAL_CXX0X__=1" define="_GLIBCXX_USE_C99=1" define=BOOST_NO_CXX11_HDR_TYPE_TRAITS define=BOOST_NO_CXX17_HDR_FILESYSTEM define=BOOST_BIND_GLOBAL_PLACEHOLDERS define=BOOST_NO_CXX17_HDR_MEMORY_RESOURCE define=BOOST_NO_STD_WSTRING define=BOOST_NO_CXX11_HDR_TYPE_TRAITS  
  
I also tried modifying the jamfile to use the -O3 flag in the following way:  
  
project /boost/random/test   
	: requirements   
		<toolset>msvc:<define>_SCL_SECURE_NO_WARNINGS  
		<toolset>gcc:<cxxflags>"-O3"  
		<toolset>qcc:<cxxflags>"-O3"  
		<toolset>q++:<cxxflags>"-O3"  
	;    
  
Please let me know what I am doing wrong in applying this to my build.
