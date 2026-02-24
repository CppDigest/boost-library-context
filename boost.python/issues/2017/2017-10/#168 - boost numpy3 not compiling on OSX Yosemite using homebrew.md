# #168 - boost numpy3 not compiling on OSX Yosemite using homebrew. [Open]

> Username: jwag  
> Created at: 2017-10-30 01:48:27 UTC  
> Updated at: 2018-09-18 15:48:46 UTC  
> Url: https://github.com/boostorg/python/issues/168  

I get the following runtime error:  
Symbol not found: _PyClass_Type  
  Referenced from: /usr/local/opt/boost-python/lib/libboost_python-mt.dylib  
  Expected in: flat namespace  
 in /usr/local/opt/boost-python/lib/libboost_python-mt.dylib  
  
built on mac with brew:  
brew install boost --c++11  
brew install boost-python --with-python3 --c++11  
  
This is very similar to issue #96, but my problem seems to be that the boost_numpy3 libraries are not being built.  
In /usr/local/Cellar/boost-python/1.65.1/lib there are the numpy libraries but not the numpy3 libraries.  
  
libboost_numpy-mt.a  
libboost_python-mt.a  
libboost_python3-mt.a  
libboost_numpy-mt.dylib  
libboost_python-mt.dylib  
libboost_python3-mt.dylib  
libboost_numpy.a  
libboost_python.a  
libboost_python3.a  
libboost_numpy.dylib  
libboost_python.dylib  
libboost_python3.dylib  
  
It looked to me like this issue should have been merged in with the 1.65 release, but maybe I'm missing something.  
  
Thanks for the help

---

## Comment 1

> Username: zdxpan  
> Created at: 2018-09-18 15:48:46 UTC  
> Url: https://github.com/boostorg/python/issues/168#issuecomment-422447311  

我也遇到了这个问题，目前无解，实在不想放弃使用Python3来运行caffe,但是python2没有这个问题
