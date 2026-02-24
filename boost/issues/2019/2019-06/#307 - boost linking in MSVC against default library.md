# #307 - boost linking in MSVC  against default  library [Open]

> Username: mayadav1  
> Created at: 2019-06-26 13:05:28 UTC  
> Updated at: 2019-07-17 13:27:30 UTC  
> Url: https://github.com/boostorg/boost/issues/307  

I have been linking boost library from CMD but MSVC linked  standard library and boost library remains unused.  
Followed steps:  
cl /F 10000000 /TP /EHsc random_speed.cpp /I C:\Users\zen2-\MS_COMPILER\memcpy_boost_18062019\install\include\boost-1_70\ /link /LIBPATH:C:\Users\zen2-\MS_COMPILER\memcpy_boost_18062019\install\lib\ libboost_random-vc142-mt-x64-1_70.lib libboost_filesystem-vc142-mt-x64-1_70.lib.  
Please help me out.

---

## Comment 1

> Username: mjcaisse  
> Created at: 2019-06-26 15:44:16 UTC  
> Url: https://github.com/boostorg/boost/issues/307#issuecomment-505932288  

Hi @mayadav1 -  
  
I'm not certain what you are asking.

---

## Comment 2

> Username: mayadav1  
> Created at: 2019-06-26 16:01:54 UTC  
> Url: https://github.com/boostorg/boost/issues/307#issuecomment-505939217  

Hi @mjcaisse   
  
I was trying to link boost library using MSVC from CMD but MSVC is resolving symbols from system library in place of from boost library.  Used steps are as follows :  
cl /F 10000000 /TP /EHsc random_speed.cpp /I C:\Users\zen2-\MS_COMPILER\memcpy_boost_18062019\install\include\boost-1_70\ /link /LIBPATH:C:\Users\zen2-\MS_COMPILER\memcpy_boost_18062019\install\lib\ libboost_random-vc142-mt-x64-1_70.lib libboost_filesystem-vc142-mt-x64-1_70.lib.

---

## Comment 3

> Username: mayadav1  
> Created at: 2019-07-17 13:27:30 UTC  
> Url: https://github.com/boostorg/boost/issues/307#issuecomment-512253839  

VS2019 processes standard/system library in place of boost library. After following changes in VS2019.  
BOOST's header files Path :   
project properties -> VC++ directories-> include directories.  
BOOST's library PATH :   
project properties -> VC++ directories-> libraries directories.  
BOOST's libraries PATH :   
Linker->General->Additional library directories.  
Ideally It should process "libboost_filesystem-vc142-mt-x64-1_70.lib" but in build order log no such linked library. I am suspecting that it is resolving calls from standard library.  
Please correct me in case wrong.
