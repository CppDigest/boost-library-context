# #186 - HRESULT was unexpected at this time [Closed]

> Username: OlafvdSpek  
> Created at: 2017-03-28 07:11:13 UTC  
> Updated at: 2017-03-30 12:21:27 UTC  
> Closed at: 2017-03-30 12:21:27 UTC  
> Url: https://github.com/boostorg/build/issues/186  

VS2015 / W10:  
  
```  
PS C:\vc\boost_1_64_0> .\bootstrap.bat  
  
Building Boost.Build engine  
HRESULT was unexpected at this time.  
  
PS C:\vc\boost_1_64_0>  
```  
  
It seems to be this bit of code that causes the error but I have no clue why.  
  
```  
if "_%BOOST_JAM_TOOLSET_ROOT%_" == "__" (  
    if NOT "_%VS150COMNTOOLS%_" == "__" (  
        set "BOOST_JAM_TOOLSET_ROOT=%VS150COMNTOOLS%..\..\VC\"  
    ))  
```  
  
Hmm  
  
echo "_%BOOST_JAM_TOOLSET_ROOT%_"  
  
```  
"_Exception calling "Query" with "1" argument(s): "Error HRESULT  
E_FAIL has been returned from a call to a COM component."\VC\_"  
```

---

## Comment 1

> Username: KindDragon  
> Created at: 2017-03-30 08:51:25 UTC  
> Url: https://github.com/boostorg/build/issues/186#issuecomment-290345299  

I think [cl_path.cmd](https://github.com/boostorg/build/blob/61792c1a9a1392a8b73231667abb7f780d2e1864/src/tools/vc141helper/cl_path.cmd) return error string without setting exit code
