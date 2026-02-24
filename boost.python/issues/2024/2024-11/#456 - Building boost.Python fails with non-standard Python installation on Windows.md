# #456 - Building boost.Python fails with non-standard Python installation on Windows [Open]

> Username: cschadl  
> Created at: 2024-11-04 22:51:25 UTC  
> Updated at: 2024-11-05 03:10:53 UTC  
> Url: https://github.com/boostorg/python/issues/456  

I'm able to successfully build boost.Python on my local machine with Python 3.13 using b2 like so:  
```  
.\bootstrap.bat vc143  
.\b2 python=3.13 # other arguments  
```  
  
The issues start happening when I try to build on our Azure DevOps build agents. Because of _.\*\~reasons\~\*._ I can't install Python 3.13 directly on those build agents. So, I zipped up a python 3.13 installation, and in the build pipeline I grab the Python zip file and extract it to some directory, say, `Z:\Python313`. Then I created a user-config.jam in the root of the boost directory like the following:  
  
```  
using python : 3.13 : Z:\Python313 ;  
```  
  
And in the build pipeline, I try to build boost.Python like so  
  
```  
bootstrap.bat vc143 --with-python=3.13  
b2 python=3.13  
```  
  
While bootstrap.bat succeeds, b2 fails with the following error:  
```  
error: "3.13" is not a known value of feature <python>  
error: legal values: "3.9"  
```  
  
So I understand that the way I'm trying to do this is a little bit unusual, but is there a way that I can get this to work? Or must Python be installed via the installer (because b2 uses `py` or the registry entries that the installer creates) to build boost.Python on Windows?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-11-05 03:10:52 UTC  
> Url: https://github.com/boostorg/python/issues/456#issuecomment-2456137781  

I unfortunately don't have any Windows machine and my understanding of `b2` is extremely limited. Perhaps @grafikrobot can help ?
