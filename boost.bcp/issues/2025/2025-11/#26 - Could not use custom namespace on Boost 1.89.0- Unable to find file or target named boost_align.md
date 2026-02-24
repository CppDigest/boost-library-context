# #26 - Could not use custom namespace on Boost 1.89.0: Unable to find file or target named boost_align [Open]

> Username: uilianries  
> Created at: 2025-11-27 09:56:31 UTC  
> Updated at: 2025-11-27 09:56:31 UTC  
> Url: https://github.com/boostorg/bcp/issues/26  

Hello!  
  
I am opening this issue as a reference to https://github.com/conan-io/conan-center-index/issues/28978  
  
An error was reported when trying to use Boost custom namespace, but of course, it could be a bug in the Conan recipe.   
  
Still, I tried to build from scratch without Conan, and I reached the following steps:  
  
```bash  
#!/bin/bash  
  
BOOST_VERSION=1_89_0  
BOOST_BASE_VERSION=$(echo ${BOOST_VERSION} | tr _ .)  
MYBOOST_DIR=/tmp/boost-${BOOST_BASE_VERSION}/myboost  
INSTALL_DIR=/tmp/boost-${BOOST_BASE_VERSION}/install  
  
set -ex  
  
wget https://archives.boost.io/release/${BOOST_BASE_VERSION}/source/boost_${BOOST_VERSION}.tar.bz2  
tar -xjf boost_${BOOST_VERSION}.tar.bz2  
cd boost_${BOOST_VERSION}  
  
./bootstrap.sh --with-toolset=gcc  
./b2 tools/bcp  
  
rm -rf ${MYBOOST_DIR}  
mkdir -p ${MYBOOST_DIR}  
./dist/bin/bcp --boost=. --namespace=myboost \  
  align atomic charconv chrono container context contract coroutine \  
  date_time exception fiber filesystem graph iostreams json \  
  locale log math nowide process program_options random regex \  
  serialization stacktrace test thread timer type_erasure url wave \  
  config build boost_install headers \  
  ${MYBOOST_DIR}  
  
cd ${MYBOOST_DIR}  
./bootstrap.sh --with-toolset=gcc  
  
./b2 \  
  toolset=gcc \  
  variant=release \  
  link=static \  
  threading=multi \  
  cxxstd=17 \  
  --prefix=${INSTALL_DIR} \  
  install  
```  
  
However, in the final steps, when running b2 in the new subset folder, I have:  
  
```  
error: Unable to find file or target named  
error:     '/boost/align//boost_align'  
error: referred to from project at  
error:     '/tmp/boost-1.89.0/myboost/libs/atomic/build'  
```  
  
You can read my full log here: [boost-1.89.0-build-namespace.log](https://github.com/user-attachments/files/23789244/boost-1.89.0-build-namespace.log)  
  
It seems to be related to the modular Boost, causing a missing dependency. But as I'm not sure about it, I opened this issue first to get some support on this case.   
  
#### Environment  
  
Boost version: 1.89.0  
OS: GNU/Linux 6.8.0  
Distro: Ubuntu 24.04  
Compiler: gcc (Ubuntu 11.4.0-1ubuntu1~22.04.2) 11.4.0  
  
Please, let me know if you need extra information that can help to clarify the case. Regards!
