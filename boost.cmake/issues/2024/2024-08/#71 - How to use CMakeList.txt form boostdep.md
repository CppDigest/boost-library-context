# #71 - How to use CMakeList.txt form boostdep? [Open]

> Username: seintcat  
> Created at: 2024-08-19 09:23:27 UTC  
> Updated at: 2024-08-19 09:23:27 UTC  
> Url: https://github.com/boostorg/cmake/issues/71  

I'm looking proper way to apply library in my project with assets that require some Boost module.  
  
Asset using some modules in CMakeList.txt  
target_link_libraries(someAsset  
	INTERFACE  
		Boost::iostreams  
		Boost::~~  
)  
  
CMake after version 3.30, find_package not working, so I tried boostdep, and get some content of CMakeList.txt  
Result contains CMAKE_MODULE_PATH, so it seems to be in cloned repository but where can I paste file?   
I can't find any notice about purpose path or auto generating.
