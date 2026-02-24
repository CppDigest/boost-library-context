# #149 - failed to install boost.python, ImportError: numpy.core.multiarray failed to import [Open]

> Username: neal668  
> Created at: 2017-08-21 10:46:31 UTC  
> Updated at: 2019-03-04 03:10:14 UTC  
> Url: https://github.com/boostorg/python/issues/149  

I tried to install the boost.python on centos6 system. the naive python is 2.6.6 while  
I installed one python2.7.5 version on this system as well as other dependencies.  
  
however, when installing boost.python, executing "scons config"  
it reported my numpy is missing while I installed the numpy in location   
/usr/local/python2.7/lib/python2.7/site-packages/numpy  
  
**Checking for C++11 support...no  
Checking for Python...yes  
ImportError: numpy.core.multiarray failed to import  
Checking for NumPy...no**  
  
please give some suggestions how to fix this, I tried to link the numpy.py in config,  
`numpy_source_file = r"""`  
while it seems didn't work or perhaps I set a wrong value to this link #

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-03-04 03:10:14 UTC  
> Url: https://github.com/boostorg/python/issues/149#issuecomment-469103385  

Can you please confirm whether this is still an issue with current versions (`develop`, or a recent release) ? I applied a few fixes that may affect this issue (e.g. https://github.com/boostorg/python/commit/ed4776b59caec6dfbea548a96701a810653e6f24), and would appreciate a test so I can close the issue if possible. Thanks.
