# #796 - boost_python3 and boost_numpy3 to reference to both Python3 as well as Python2 [Closed]

> Username: npandey2385  
> Created at: 2018-10-16 09:59:42 UTC  
> Updated at: 2018-10-17 01:21:26 UTC  
> Closed at: 2018-10-17 01:20:44 UTC  
> Url: https://github.com/boostorg/compute/issues/796  

I build boost with Python 3 (3.6.1) but I see some issue  
  
ldd boost/v1.63.0p1/lib/libboost_numpy3.so.1.63.0  
  
libboost_python.so.1.63.0 => not found  
  
My concern is with this line:  
  
libboost_python.so.1.63.0 => not found  
  
I would expect to be compiled against  
  
libboost_python3.so.1.63.0 => /boost/v1.63.0p1/lib/libboost_python3.so.1.63.0  
  
I used the following configuration to compile it with python 3  
  
Configure specific Python version.(user-config.jam)  
using python : 3.6 : python/v3.6.1/bin/python3 : python/v3.6.1/include/python3.6m : python/v3.6.1/lib ;  
  
Here are the commands I used to build this:  
  
1- ./bootstrap.sh --prefix=/tools/boost --with-python=/python/v3.6.1/bin/python3 --with-python-version=3.6 --with-python-root=/python/v3.6.1  
  
2- ./b2 install --config=user-config.jam  
  
I am trying to build boost 1.63.0

---

## Comment 1

> Username: jszuppe  
> Created at: 2018-10-17 01:20:44 UTC  
> Updated at: 2018-10-17 01:21:26 UTC  
> Url: https://github.com/boostorg/compute/issues/796#issuecomment-430455844  

You should not report Boost Python (or other Boost libs, or general Boost build system) issues to Boost.Compute issue list.
