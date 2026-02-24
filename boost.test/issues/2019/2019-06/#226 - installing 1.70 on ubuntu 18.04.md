# #226 - installing 1.70 on ubuntu 18.04 [Closed]

> Username: asdfzxh8  
> Created at: 2019-06-06 05:35:46 UTC  
> Updated at: 2019-06-07 08:28:44 UTC  
> Closed at: 2019-06-07 08:28:44 UTC  
> Url: https://github.com/boostorg/test/issues/226  

sudo apt install libboost-dev would install the 1.65 version on ubuntu 18.04. Is that the newest version for the system?  
  
I tried   
wget https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.tar.gz  
tar -zxvf boost_1_70_0.tar.gz  
cd boost_1_70_0/  
./bootstrap.sh  
  
but it showed   
'Building Boost.Build engine with toolset ...   
Failed to build Boost.Build build engine  
Consult 'bootstrap.log' for more details'?  
Can anyone help? Thanks.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-06-06 05:37:57 UTC  
> Url: https://github.com/boostorg/test/issues/226#issuecomment-499355355  

Maybe your system does not have the compiler installed? do you have gcc/g++ or clang/clang++ installed? Otherwise you can install a minimal compiler with `sudo apt-get install build-essential`

---

## Comment 2

> Username: asdfzxh8  
> Created at: 2019-06-06 15:37:58 UTC  
> Updated at: 2019-06-06 17:42:54 UTC  
> Url: https://github.com/boostorg/test/issues/226#issuecomment-499548733  

Thank you very much. It installed using ./b2. However, it still doesn't seem to be work:  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    /root/boost_1_70_0  
  
The following directory should be added to linker library paths:  
  
    /root/boost_1_70_0/stage/lib  
  
root@ubuntutest:~/boost_1_70_0# export INCLUDE="/root/boost_1_70_0:$INCLUDE"  
root@ubuntutest:~/boost_1_70_0# export LIBRARY_PATH="/root/boost_1_70_0/stage/lib:$LIBRARY_PATH"  
root@ubuntutest:~/boost_1_70_0# dpkg -s libboost-dev | grep Version  
dpkg-query: package 'libboost-dev' is not installed and no information is available  
Use dpkg --info (= dpkg-deb --info) to examine archive files,  
and dpkg --contents (= dpkg-deb --contents) to list their contents.
