# #28 - Android Issues [Open]

> Username: seanh1986  
> Created at: 2019-05-27 23:01:53 UTC  
> Updated at: 2019-05-27 23:02:31 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/28  

Hi,  
  
I am trying to integrate boost into an android application for use with the Android NDK. I am trying to integrate boost 1.70.  
  
I have tried two different configurations (both on Android):  
  
**Configuration 1**  
Android Gradle Plugin version: 3.4.1  
Gradle version: 5.1.1  
CMake version: 3.14.4  
Android NDK version: 19.2.5345600  
  
**Configuration 2**  
I upgraded to the latest beta versions of all the above, so 3.5.+, etc.  
  
Both are using Clang compilers. I built the boost library in two different ways... Using the following two repositories:  
https://github.com/Orphis/boost-cmake  
https://github.com/moritz-wundke/Boost-for-Android  
  
In all cases (both configurations and both build types), I was able to successfully generate the boost files and import them into my project.  
  
The only boost class that I require is boost::circular_buffer (and it's corresponding classes it references).  
  
Basically, boost itself seems to be missing includes that it needs.  
  
In the file boost/circular_buffer/base.hpp, I have the following issues  
- Missing include for details.hpp, otherwise it cannot find "cb_details::const_traits"  
- "BOOST_CB_ASSERT" and ".is_valid( ... )" all seem to be missing imports, etc.  
  
Note: other issues exist in other files, as well. I tried manually adding the 'recommended' imports, but that just caused a chain reaction of missing imports that I couldn't resolve.  
  
I tried using the tagged 1.70 version so I expected it to be stable.  
  
Thanks for your help!  
  
![image](https://user-images.githubusercontent.com/15824440/58441625-7579e400-80b1-11e9-9cef-91507e0f7d6b.png)

---

## Comment 1

> Username: seanh1986  
> Created at: 2019-05-27 23:02:31 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/28#issuecomment-496317894  

Here is another screen shot showing the issue.  
  
![image](https://user-images.githubusercontent.com/15824440/58441683-e7522d80-80b1-11e9-92bc-ba6379722144.png)
