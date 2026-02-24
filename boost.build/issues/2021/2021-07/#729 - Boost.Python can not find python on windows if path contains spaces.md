# #729 - Boost.Python can not find python on windows if path contains spaces [Open]

> Username: Grantim  
> Created at: 2021-07-07 16:07:11 UTC  
> Updated at: 2022-08-22 18:40:09 UTC  
> Url: https://github.com/boostorg/build/issues/729  

This issue was mentioned [here](https://github.com/microsoft/vcpkg/issues/5732), when building boost.python with numpy.  
Everything is ok if python is installed in path without spaces, and it does not find system python (and thereafter numpy) if spaces are present in the path. So attached [file](https://github.com/boostorg/build/files/6778610/FixSpacesInPathIssue.patch.txt) can be applied as patch (mb it is not the best fix, but still works, please have a look).   
  
I faced this issue on boost version 1.75.0

---

## Comment 1

> Username: pavel-machyniak  
> Created at: 2022-08-22 18:40:08 UTC  
> Url: https://github.com/boostorg/build/issues/729#issuecomment-1222768879  

I have the same issue with current boost 1.80.  
I just need to build the boost libraries on Windows (using vc143) and choose correct python interpreter.  
I have 32-bit python installed in standard location:   
`C:\Program Files (x86)\Python310-32`  
There is no python configured on system path nor in system variables.  
I _bootstrap_ the _boost_ and enter python configuration either to `project-config.jam` or to `tools\build\src\user-config.jam` or `tools\build\src\site-config.jam`:  
```  
using python : : "C:/Program Files (x86)/Python310-32/python.exe" ;  
```  
or:  
```  
using python : : "C:\\Program Files (x86)\\Python310-32\\python.exe" ;  
```  
  
Result:  
```  
notice: [python-cfg] Configuring python...  
notice: [python-cfg]   user-specified cmd-or-prefix: "C:/Program Files (x86)/Python310-32/python.exe"  
notice: [python-cfg] Checking interpreter command "C:/Program Files (x86)/Python310-32/python.exe"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\Program Files (x86)\Python310-32\python.exe" 2>&1'  
notice: [python-cfg] running command 'C:/Program Files (x86)/Python310-32/python.exe -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...does not invoke a working interpreter  
```  
  
Note that:  
- configuration and path is picked up correctly  
- interpreter command is checked correctly  
- but when running python script via command line parameter, interpreter is run incorrectly without `""`, so it is probably trying to run `C:/Program`  
  
That being said, there is definitely problem with spaces in the path. When I change the setting to ancient 8.3 compatibility format:  
```  
using python : : c:/PROGRA~2/PYTHON~1/python.exe ;  
```  
  
Then it runs correctly:  
```  
notice: [python-cfg] Configuring python...  
notice: [python-cfg]   user-specified cmd-or-prefix: "c:/PROGRA~2/PYTHON~1/python.exe"  
notice: [python-cfg] Checking interpreter command "c:/PROGRA~2/PYTHON~1/python.exe"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\Program Files (x86)\Python310-32\python.exe" 2>&1'  
notice: [python-cfg] running command 'c:/PROGRA~2/PYTHON~1/python.exe -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "c:/PROGRA~2/PYTHON~1/python.exe"  
notice: [python-cfg]   include path: "c:\PROGRA~2\PYTHON~1\Include"  
notice: [python-cfg]   library path: "c:\PROGRA~2\PYTHON~1\libs"  
notice: [python-cfg]   DLL search path: "c:\PROGRA~2\PYTHON~1"  
```
