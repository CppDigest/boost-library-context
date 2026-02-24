# #601 - project-config.jam missing path to python interpreter (boost 1.78.0) [Open]

> Username: nicudo  
> Created at: 2022-01-05 22:10:45 UTC  
> Updated at: 2022-01-05 22:10:45 UTC  
> Url: https://github.com/boostorg/boost/issues/601  

project-config.jam generated is missing the path to the interpreter, and therefore b2 will fail to build the python3 libraries.  
  
Simplified example below.  
  
Running:  
```  
./bootstrap.sh --with-python=/Library/Frameworks/Python.framework/Versions/3.10/bin/python3  
```  
  
will generate the line:  
```  
using python : 3.10 : "/Library/Frameworks/Python.framework/Versions/3.10" ;  
```  
  
which will fail when building, because it is invoking "python" instead of "python3" even if it was specified in in the bootstrap command:  
```  
notice: [python-cfg] Checking interpreter command "/Library/Frameworks/Python.framework/Versions/3.10/bin/python"...  
notice: [python-cfg] running command '/Library/Frameworks/Python.framework/Versions/3.10/bin/python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...does not invoke a working interpreter  
```
