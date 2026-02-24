# #246 - Building for Python3.6 - ImportError: undefined symbol: PyClass_Type [Open]

> Username: tnugent97  
> Created at: 2018-12-23 13:38:07 UTC  
> Updated at: 2019-11-28 17:27:29 UTC  
> Url: https://github.com/boostorg/python/issues/246  

Hi,  
  
I am trying to build boost for use with Python 3 but I am having some troubles.  
I call `./bootstrap --with-python-version=3.6` and then `./b2 --with-python --debug-configuration` which correctly shows python 3.6 is being used and the python component is being built.  
  
When running the `$BOOST_ROOT/libs/pythion/example/tutorial`, I get the following error:  
```ImportError: /usr/lib/x86_64-linux-gnu/libboost_python-py27.so.1.65.1: undefined symbol: PyClass_Type```  
  
This shows the python2.7 shared object is being used and I am aware that the PyClass_Type is a Python 2 type. So somehow the program is still being built with python 2 despite me specifying Python 3.  
I have tried several iterations of cleaning and building all, just in case something had not been built properly, but I still get the same issue.   
  
Am I missing something ?  
  
Thanks

---

## Comment 1

> Username: Kischy  
> Created at: 2019-11-28 17:27:29 UTC  
> Url: https://github.com/boostorg/python/issues/246#issuecomment-559567984  

Does anyone know a solution do this error?
