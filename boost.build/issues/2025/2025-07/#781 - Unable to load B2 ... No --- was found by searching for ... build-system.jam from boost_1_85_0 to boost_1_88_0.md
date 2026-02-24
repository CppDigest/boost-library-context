# #781 - Unable to load B2 ... No *** was found by searching for ... build-system.jam from boost_1_85_0 to boost_1_88_0 [Closed]

> Username: PlutusCN  
> Created at: 2025-07-01 12:29:03 UTC  
> Updated at: 2025-07-01 12:29:12 UTC  
> Closed at: 2025-07-01 12:29:11 UTC  
> Url: https://github.com/boostorg/build/issues/781  

Hi, everyone   
  
        I have tired to compile boost from boost_1_81_0 to boost_1_88_0 with MSYS2 by getting source code from https://www.boost.org/releases/latest/  
  
        And I got two kinds of result:  
        (1) That is fine to be compiled from boost_1_81_0 to boost_1_84_0;  
        (2) but there is some problem from boost_1_84_0 to boost_1_88_0 like following   
  
**# ./b2 -j4 toolset=gcc address-model=64 link=static,shared threading=multi --build-type=complete  
Unable to load B2  
-----------------  
No '' was found by searching for:  
  E:/Dev/study/open_source/boost/boost_1_88_0/.b2/build-system.jam  
  E:/Dev/study/open_source/boost/share/b2/build-system.jam  
  E:/Dev/study/open_source/boost/share/b2/src/build-system.jam  
  E:/Dev/study/open_source/boost/build-system.jam  
  E:/Dev/study/open_source/boost/boost_1_88_0/src/build-system.jam  
  E:/Dev/study/open_source/boost/boost_1_88_0/tools/build/src/build-system.jam  
  E:/Dev/study/open_source/boost/boost_1_88_0/src/build-system.jam  
  E:/Dev/study/open_source/boost/src/build-system.jam  
  E:/Dev/study/open_source/src/build-system.jam  
  E:/Dev/study/src/build-system.jam  
  E:/Dev/src/build-system.jam  
  E:/src/build-system.jam  
  
Please consult the documentation at 'https://www.bfgroup.xyz/b2/'.**  
  
Before that, I already run: **./bootstrap.sh --with-toolset=gcc --with-libraries=all --prefix=<where-to-install>** successfully.  
  
So, the question is why the compilation problem happen and how to fix it ?  
  
Thanks a lot !

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2025-07-01 12:29:10 UTC  
> Url: https://github.com/boostorg/build/issues/781#issuecomment-3023812743  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
