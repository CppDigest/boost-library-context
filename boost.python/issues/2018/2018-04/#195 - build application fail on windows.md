# #195 - build application fail on windows [Closed]

> Username: liuaifu  
> Created at: 2018-04-18 07:43:23 UTC  
> Updated at: 2018-04-19 01:32:27 UTC  
> Closed at: 2018-04-19 01:32:27 UTC  
> Url: https://github.com/boostorg/python/issues/195  

Today, i download boost1.67 release replace 1.66 on windows 7 & vs2017, compile it, get:  
boost_python36-vc141-mt-gd-x32-1_67.dll  
boost_python36-vc141-mt-gd-x32-1_67.lib  
libboost_python36-vc141-mt-gd-x32-1_67.lib  
and other libraries.  
But build my own app fail:  
  
    LNK1104	can't open file boost_pythonPY_MAJOR_VERSIONPY_MINOR_VERSION-vc141-mt-gd-x32-1_67.lib  
  
The app is build corrent on boost1.66.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-04-19 01:32:27 UTC  
> Url: https://github.com/boostorg/python/issues/195#issuecomment-382580638  

This is a duplicate of https://github.com/boostorg/python/issues/193.
