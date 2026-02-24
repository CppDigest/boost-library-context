# #292 - Error C3861 'to_python_use_handle_instead': identifier not found [Closed]

> Username: rathaROG  
> Created at: 2019-05-20 09:51:38 UTC  
> Updated at: 2019-05-21 14:14:00 UTC  
> Closed at: 2019-05-21 14:14:00 UTC  
> Url: https://github.com/boostorg/boost/issues/292  

I used the latest prebuilt windows binaries v1.70.0, and I had two issues when building my project. Are they the bug of this version or I did something wrong?  
```  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C3861	'to_python_use_handle_instead': identifier not found	TMP	C:\dev\proj\dependencies\boost\boost\python\converter\arg_to_python.hpp	177	  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C2027	use of undefined type 'boost::python::converter::detail::cannot_convert_raw_PyObject<T *>'	TMP	C:\dev\proj\dependencies\boost\boost\python\converter\arg_to_python.hpp	177	  
```

---

## Comment 1

> Username: rathaROG  
> Created at: 2019-05-21 14:14:00 UTC  
> Url: https://github.com/boostorg/boost/issues/292#issuecomment-494409499  

Solved! Find explanation here: https://stackoverflow.com/a/48430513/5167654
