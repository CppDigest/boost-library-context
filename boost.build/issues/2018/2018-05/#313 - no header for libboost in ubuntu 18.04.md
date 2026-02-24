# #313 - no header for libboost in ubuntu 18.04 [Open]

> Username: Cdriko  
> Created at: 2018-05-24 08:59:06 UTC  
> Updated at: 2021-06-26 03:09:57 UTC  
> Url: https://github.com/boostorg/build/issues/313  

hello  
I successfull compile antimony under ubuntu 16.04,  
but with ubuntu 18.04, after installing all the necessaries dependencies, I get  :   
```  
cmake GNinja ..  
CMake Warning at /usr/share/cmake-3.10/Modules/FindBoost.cmake:1626 (message):  
  No header defined for python3; skipping header check  
Call Stack (most recent call first):  
  CMakeLists.txt:28 (find_package)  
  
  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /home/cdoutriaux/Documents/soft/antimony/build  
  
```  
  
even this check  
`dpkg -l libboost-all-dev `  
  
give :   
  
```  
||/ Nom                             Version              Architecture         Description  
+++-===============================-====================-====================-===================================================================  
ii  libboost-all-dev                1.65.1.0ubuntu1      amd64                Boost C++ Libraries development files (ALL) (default version)  
```  
have you got an idea for fix this problem ?  
the problem seems to be near like in redhat #178

---

## Comment 1

> Username: jwakely  
> Created at: 2018-08-23 10:42:11 UTC  
> Url: https://github.com/boostorg/build/issues/313#issuecomment-415371718  

This doesn't seem like a problem with Boost, you should contact Ubuntu.  
  
> the problem seems to be near like in redhat #178  
  
Wrong issue number?

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:22 UTC  
> Url: https://github.com/boostorg/build/issues/313#issuecomment-868936418  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
