# #703 - Window build error [Closed]

> Username: sdarwin  
> Created at: 2021-01-20 14:33:44 UTC  
> Updated at: 2021-01-27 03:12:47 UTC  
> Closed at: 2021-01-20 22:17:43 UTC  
> Url: https://github.com/boostorg/build/issues/703  

Until recently builds on Microsoft Windows were working fine. Now an error appears.    
```  
execunix.cpp:20:10: fatal error: sys/resource.h: No such file or directory  
 #include <sys/resource.h>  
          ^~~~~~~~~~~~~~~~  
compilation terminated.  
```  
  
Notice execunix.cpp, which is **unix** instead of **nt**.   
  
In commit 98a6f949 a section of src/engine/build.sh was removed.     
Would it be ok to revert this change?  
  
```  
case $B2_OS in  
    NT)  
    B2_SOURCES="${B2_SOURCES} execnt.cpp filent.cpp pathnt.cpp"  
    ;;  
  
    *)  
    B2_SOURCES="${B2_SOURCES} execunix.cpp fileunix.cpp pathunix.cpp"  
    ;;  
esac  
```

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-01-20 15:48:50 UTC  
> Updated at: 2021-01-20 16:14:58 UTC  
> Url: https://github.com/boostorg/build/issues/703#issuecomment-763725258  

What script and shell are you using for the build? Also, which compiler? I.e. what command do you run to build?

---

## Comment 2

> Username: sdarwin  
> Created at: 2021-01-20 16:29:10 UTC  
> Updated at: 2021-01-20 16:31:48 UTC  
> Url: https://github.com/boostorg/build/issues/703#issuecomment-763763728  

For example:    
```  
bash.exe boost-script.sh    
```  
Where boost-script.sh is https://github.com/boostorg/json/blob/develop/.drone/boost-script.sh  
  
This was based on a pre-existing .travis.yml file   
https://github.com/boostorg/json/blob/ec44f5ce320664bf1d38efde8bd31bf57b98691a/.travis.yml  
  
So, eventually it's calling boostorg/boost-ci/ci/common_install.sh    
  
The advantage is... that the same exact build script can be used for all operating systems.  From the end-user perspective,  simple and consistent.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2021-01-20 16:48:17 UTC  
> Url: https://github.com/boostorg/build/issues/703#issuecomment-763778166  

Okay.. so the use case is running bash with mingw gcc compiler.

---

## Comment 4

> Username: sdarwin  
> Created at: 2021-01-21 00:14:00 UTC  
> Url: https://github.com/boostorg/build/issues/703#issuecomment-764057882  

@grafikrobot  Thank you!!

---

## Comment 5

> Username: sdarwin  
> Created at: 2021-01-21 21:39:57 UTC  
> Updated at: 2021-01-21 21:43:37 UTC  
> Url: https://github.com/boostorg/build/issues/703#issuecomment-764958248  

Now there's a new error on the Windows builds.  
  
```  
++ ./b2 headers  
Unable to load B2: could not find 'boost-build.jam'  
---------------------------------------------------  
Attempted search from 'C:\drone\boost-root' up to the root at 'C:\drone\boost-root\b2.exe'  
Please consult the documentation at 'https://boostorg.github.io/build/'  
```  
Added some debugging, to print the working directory and directory contents.  
```  
++ pwd  
  
/c/drone/boost-root  
  
++ ls -al   
  
total 451  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:12 .  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 ..  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 .circleci  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:10 .git  
-rw-r--r-- 1 ContainerAdministrator 380929   4013 Jan 21 21:09 .gitattributes  
-rw-r--r-- 1 ContainerAdministrator 380929    173 Jan 21 21:09 .gitignore  
-rw-r--r-- 1 ContainerAdministrator 380929  18863 Jan 21 21:09 .gitmodules  
-rw-r--r-- 1 ContainerAdministrator 380929   3740 Jan 21 21:09 .travis.yml  
-rw-r--r-- 1 ContainerAdministrator 380929    484 Jan 21 21:09 CMakeLists.txt  
-rw-r--r-- 1 ContainerAdministrator 380929    298 Jan 21 21:09 INSTALL  
-rw-r--r-- 1 ContainerAdministrator 380929  12276 Jan 21 21:09 Jamroot  
-rw-r--r-- 1 ContainerAdministrator 380929   1361 Jan 21 21:09 LICENSE_1_0.txt  
-rw-r--r-- 1 ContainerAdministrator 380929   1594 Jan 21 21:09 README.md  
-rw-r--r-- 1 ContainerAdministrator 380929   1253 Jan 21 21:09 appveyor.yml  
-rwxr-xr-x 1 ContainerAdministrator 380929 276992 Jan 21 21:12 b2.exe  
-rw-r--r-- 1 ContainerAdministrator 380929    867 Jan 21 21:09 boost-build.jam  
-rw-r--r-- 1 ContainerAdministrator 380929   1054 Jan 21 21:09 boost.css  
-rw-r--r-- 1 ContainerAdministrator 380929   6308 Jan 21 21:09 boost.png  
-rw-r--r-- 1 ContainerAdministrator 380929  20684 Jan 21 21:09 boostcpp.jam  
-rw-r--r-- 1 ContainerAdministrator 380929   2497 Jan 21 21:09 bootstrap.bat  
-rwxr-xr-x 1 ContainerAdministrator 380929  10458 Jan 21 21:09 bootstrap.sh  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 doc  
-rw-r--r-- 1 ContainerAdministrator 380929    794 Jan 21 21:09 index.htm  
-rw-r--r-- 1 ContainerAdministrator 380929   6172 Jan 21 21:09 index.html  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 libs  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 more  
-rw-r--r-- 1 ContainerAdministrator 380929    929 Jan 21 21:12 project-config.jam  
-rw-r--r-- 1 ContainerAdministrator 380929   2757 Jan 21 21:09 rst.css  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 status  
drwxr-xr-x 1 ContainerAdministrator 380929      0 Jan 21 21:09 tools  
++ echo 'About to run '\''./b2 headers'\'''  
++ ./b2 headers  
Unable to load B2: could not find 'boost-build.jam'  
---------------------------------------------------  
Attempted search from 'C:\drone\boost-root' up to the root at 'C:\drone\boost-root\b2.exe'  
Please consult the documentation at 'https://boostorg.github.io/build/'.  
```  
  
The file is there.   
  
This worked before. How about reverting to the earlier strategy?  
```  
case $B2_OS in  
    NT)  
```

---

## Comment 6

> Username: grafikrobot  
> Created at: 2021-01-27 03:12:47 UTC  
> Url: https://github.com/boostorg/build/issues/703#issuecomment-767986361  

https://github.com/boostorg/build/commit/4a7dd4937ffcc6e6ba8fdf7098f557e1642e1835
