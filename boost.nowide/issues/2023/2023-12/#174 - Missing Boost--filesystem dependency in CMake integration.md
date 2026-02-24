# #174 - Missing Boost::filesystem dependency in CMake integration [Closed]

> Username: Willem871  
> Created at: 2023-12-15 13:55:46 UTC  
> Updated at: 2023-12-15 14:26:33 UTC  
> Closed at: 2023-12-15 14:26:22 UTC  
> Url: https://github.com/boostorg/nowide/issues/174  

boost version: 1.84.0  
  
The public boost/nowide/filesystem.hpp header contains an include to boost/filesystem/path.hpp.  
Hence, the Boost::nowide CMake target should contain a PUBLIC target_link_libraries dependency to Boost::filesystem as well.  
  
Locally, I have following fix:  
```cmake  
if(BOOST_SUPERPROJECT_SOURCE_DIR OR (TARGET Boost::config AND TARGET Boost::filesystem))    
target_link_libraries(boost_nowide PUBLIC    
    Boost::config    
    Boost::filesystem    
  )    
else()    
...    
endif()  
```   
If you agree, I can make a pull request out of this.

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-12-15 14:07:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/174#issuecomment-1857944683  

I understand the confusion however in fact Boost::filesystem is not a dependency of Boost::nowide, in that the latter requires the former to function.  
  
The header you mention provides an integration with Boost::filesystem if that is used in your project. So you only need to include that header when your project uses Boost::filesystem and hence already depends on it. So there is no need to declare a dependency of Boost::nowide on it just in case it may or may not be used.  
  
I hope that clears this up.

---

## Comment 2

> Username: Willem871  
> Created at: 2023-12-15 14:26:32 UTC  
> Url: https://github.com/boostorg/nowide/issues/174#issuecomment-1857969866  

Thanks for the fast response!  
And right, I didn't understand the point of that filesystem.hpp header.  
Thanks for the explanation!
