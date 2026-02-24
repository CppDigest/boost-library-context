# #204 - header for python3 missing in ubuntu bionic [Closed]

> Username: Cdriko  
> Created at: 2018-05-18 14:01:40 UTC  
> Updated at: 2018-05-25 14:13:19 UTC  
> Closed at: 2018-05-25 14:13:19 UTC  
> Url: https://github.com/boostorg/python/issues/204  

hello, I try to[ compile a soft](https://github.com/mkeeter/antimony) that i've successfull done under ubuntu 16.04. but with 18.04 I get  
```  
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

> Username: stefanseefeld  
> Created at: 2018-05-18 14:37:08 UTC  
> Url: https://github.com/boostorg/python/issues/204#issuecomment-390227730  

Unfortunately CMake doesn't tell us all the necessary details to figure out what is missing. I assume that what's missing is the Python header itself, so you should install `python-dev` to solve this. Let me know whether that works.

---

## Comment 2

> Username: Cdriko  
> Created at: 2018-05-18 14:48:32 UTC  
> Url: https://github.com/boostorg/python/issues/204#issuecomment-390231198  

alas it seems installed :   
```  
apt-get install python-dev  
E: Impossible d'ouvrir le fichier verrou /var/lib/dpkg/lock - open (13: Permission non accordée)  
E: Impossible de verrouiller le répertoire d'administration (/var/lib/dpkg/). Avez-vous les privilèges du superutilisateur ?  
cdoutriaux@pcubuntu-cdoutriaux:~/Documents/soft/antimony/build$ dpkg --status python-dev  
Package: python-dev  
Status: install ok installed  
Priority: optional  
Section: python  
Installed-Size: 18  
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>  
Architecture: amd64  
Multi-Arch: allowed  
Source: python-defaults  
Version: 2.7.15~rc1-1  
Depends: python (= 2.7.15~rc1-1), libpython-dev (= 2.7.15~rc1-1), python2.7-dev (>= 2.7.15~rc1-1~)  
Description: header files and a static library for Python (default)  
 Header files, a static library and development tools for building  
 Python modules, extending the Python interpreter or embedding Python  
 in applications.  
 .  
 This package is a dependency package, which depends on Debian's default  
 Python version (currently v2.7).  
Original-Maintainer: Matthias Klose <doko@debian.org>  
Homepage: http://www.python.org/  
  
  
```  
  
even with python3  
```  
dpkg --status python3-dev  
Package: python3-dev  
Status: install ok installed  
Priority: optional  
Section: python  
Installed-Size: 17  
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>  
Architecture: amd64  
Multi-Arch: allowed  
Source: python3-defaults  
Version: 3.6.5-3  
Replaces: python3.1 (<< 3.1.2+20100706-3)  
Depends: python3 (= 3.6.5-3), libpython3-dev (= 3.6.5-3), python3.6-dev (>= 3.6.5-2~), python3-distutils (>= 3.6.5-2~), dh-python  
Description: header files and a static library for Python (default)  
 Header files, a static library and development tools for building  
 Python modules, extending the Python interpreter or embedding Python  
 in applications.  
 .  
 This package is a dependency package, which depends on Debian's default  
 Python 3 version (currently v3.6).  
Original-Maintainer: Matthias Klose <doko@debian.org>  
Homepage: http://www.python.org/  
  
```

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-05-19 18:20:23 UTC  
> Url: https://github.com/boostorg/python/issues/204#issuecomment-390423337  

Sorry, I'm unable to reproduce in a bionic docker container. I suggest you try to debug this yourself, perhaps by injecting messaes into the FindBoost.cmake` module to see exactly what header it is looking for and can't find.

---

## Comment 4

> Username: Cdriko  
> Created at: 2018-05-25 12:52:13 UTC  
> Url: https://github.com/boostorg/python/issues/204#issuecomment-392048392  

I finally fix this , adding the python header as python3 reference in FindBoost.cmake  
in the function  
function(_Boost_COMPONENT_HEADERS component _hdrs)  
  
[as explained here](https://gitlab.kitware.com/cmake/cmake/merge_requests/201)  
  
it seems fixed since the 3.11 version of cmake, but not in the release delivered with bionic (3.10)

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-05-25 14:13:19 UTC  
> Url: https://github.com/boostorg/python/issues/204#issuecomment-392071022  

Thanks for letting me know.
