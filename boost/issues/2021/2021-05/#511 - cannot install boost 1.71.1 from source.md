# #511 - cannot install boost 1.71.1 from source [Closed]

> Username: Atharex  
> Created at: 2021-05-03 12:31:39 UTC  
> Updated at: 2021-05-03 14:10:23 UTC  
> Closed at: 2021-05-03 14:10:23 UTC  
> Url: https://github.com/boostorg/boost/issues/511  

Hi there!  
  
I've been trying to build the boost library 1.71.1 from your releases (https://github.com/boostorg/boost/archive/refs/tags/boost-1.71.0.tar.gz) and the installation keeps failing with missing files  
  
After extracting the archive I run the following commands:  
```  
bash-4.2# cd boost-boost-1.71.0/  
bash-4.2# bash bootstrap.sh --with-libraries=all --with-toolset=gcc  
bootstrap.sh: line 196: ./tools/build/src/engine/build.sh: No such file or directory  
Building Boost.Build engine with toolset ...  
Failed to build Boost.Build build engine  
Consult 'bootstrap.log' for more details  
bash-4.2# cat bootstrap.log  
./bootstrap.sh: line 226: cd: ./tools/build/src/engine: No such file or directory  
```  
  
The log mentions missing directories. Am I missing some steps in the installation procedure or is this package missing some files?  
  
I'm running this installation on CentOS 7

---

## Comment 1

> Username: mclow  
> Created at: 2021-05-03 13:24:28 UTC  
> Url: https://github.com/boostorg/boost/issues/511#issuecomment-831257283  

The official download site is on jfrog artifactory: https://boostorg.jfrog.io/artifactory/main/release/1.71.0/source/boost_1_71_0.tar.gz

---

## Comment 2

> Username: Atharex  
> Created at: 2021-05-03 14:10:23 UTC  
> Url: https://github.com/boostorg/boost/issues/511#issuecomment-831285048  

Thanks @mclow did not know releases on this page are not the official ones. It works with the jfrog one!
