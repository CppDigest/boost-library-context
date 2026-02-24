# #401 - Can't build Boost Python 3 on macOS [Open]

> Username: light-tech  
> Created at: 2022-10-10 04:02:29 UTC  
> Updated at: 2022-11-15 22:20:06 UTC  
> Url: https://github.com/boostorg/python/issues/401  

Might be related to https://github.com/boostorg/boost/issues/503 but building Boost Python 3 (Boost 1.80.0) with  
```sh  
./bootstrap.sh --with-libraries=python --with-python=python3  
./b2 --debug-configuration --debug-building --debug-generator install  
```  
does not work: `bootstrap.sh` seems to work as it detected the right Python root location (I am using official Python installer at [python.org](https://python.org) instead of MacPort in the aforementioned issue)  
```  
Detecting Python version... 3.10  
Detecting Python root... /Library/Frameworks/Python.framework/Versions/3.10  
```  
However, `b2` reports  
```  
notice: [python-cfg] Configuring python...  
notice: [python-cfg]   user-specified version: "3.10"  
notice: [python-cfg]   user-specified cmd-or-prefix: "/Library/Frameworks/Python.framework/Versions/3.10"  
notice: [python-cfg] Checking interpreter command "/Library/Frameworks/Python.framework/Versions/3.10/bin/python"...  
notice: [python-cfg] running command '/Library/Frameworks/Python.framework/Versions/3.10/bin/python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...does not invoke a working interpreter  
```  
and apparently, the Python 3 installer on macOS does not created `bin/python`, only `bin/python3`. Shouldn't it take into account the suggested name for the Python executable in `bootstrap`? (It builds perfectly for Python 2.7 included in macOS.)
