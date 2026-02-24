# #374 - Python path detection incorrect on macOS + brew + clang, but disallowed to pass multiple lib paths via jamfile to fix issue [Open]

> Username: Yzrsah  
> Created at: 2018-11-27 20:56:41 UTC  
> Updated at: 2021-05-29 18:22:33 UTC  
> Url: https://github.com/boostorg/build/issues/374  

Hello,  
  
When building Boost on macOS Mojave with Homebrew and LLVM+Clang 7.0.0:  
  
First:  
- Install Homebrew  
- brew install llvm (includes clang 7.0.0)  
- brew install python@3  
- configure clang and environment correctly  
  
Next, build boost:  
1) `./bootstrap.sh --with-toolset=clang --with-python=/usr/local/bin/python3 --with-python-version=3.7`  
2) Small issue: This will cause the build to fail because you also have to provide "--with-python-root=/usr/local/bin/python3 " otherwise path detection is incorrect. Providing the path to the binary twice might be confusing for the user.  
  
So fixing that:  
  
1) `./bootstrap.sh --with-toolset=clang --with-python=/usr/local/bin/python3 --with-python-root=/usr/local/bin/python3 --with-python-version=3.7`  
  
Now Python paths will be detected incorrectly. (Build will fail later on):  
  
```  
notice: [python-cfg] Configuring python...  
notice: [python-cfg]   user-specified version: "3.7"  
notice: [python-cfg]   user-specified cmd-or-prefix: "/usr/local/bin/python3"  
notice: [python-cfg] Checking interpreter command "/usr/local/bin/python3"...  
notice: [python-cfg] running command '/usr/local/bin/python3 -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "/usr/local/bin/python3"  
notice: [python-cfg]   include path: "/usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/include/python3.7"  
notice: [python-cfg]   library path: "/usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib/python3.7/config" "/usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib"  
```  
  
- The detected "include path" path is incorrect, because it is missing the "m in "python3.7m"  
The correct path is `/usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/include/python3.7m`  
  
- The detected config path of the "library path" is incorrect. The correct config path should be:  
`/usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib/python3.7/config-3.7m-darwin`  
It is using only 'config' and not 'config-3.7m-darwin'  
  
Therefore I modify the project-config.jam file to add the paths manually:  
  
```  
    using python  
    	: 3.7  
    	: /usr/local/bin/python3  
    	: /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/include/python3.7m  
    	: /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib/python3.7/config-3.7m-darwin  
          /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib  
    	;  
```  
  
However this produces a build error:  
```  
*** argument error  
* rule python.init ( version ? : cmd-or-prefix ? : includes * : libraries ? : condition * : extension-suffix ? )  
* called with: ( 3.7 : /usr/local/bin/python3 : /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/include/python3.7m : /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib/python3.7/config-3.7m-darwin  
    /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib :  :  :  :  )  
* extra argument /usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib  
```  
  
Because only a single argument is allowed to be passed via libraries.  
But manually replacing all incorrectly detected paths requires passing multiple paths.

---

## Comment 1

> Username: StamKaly  
> Created at: 2019-07-04 15:46:10 UTC  
> Url: https://github.com/boostorg/build/issues/374#issuecomment-508524019  

I am having the same problem on windows using `user-config.jam`. Did you ever find a solution?

---

## Comment 2

> Username: sujit22993  
> Created at: 2020-01-31 07:08:08 UTC  
> Url: https://github.com/boostorg/build/issues/374#issuecomment-580611503  

**[Solution]**  
> 1. Updating gcc to latest version [latest releases gcc9.2 ](https://gcc.gnu.org/releases.html)  
  
> 2. Downgrading _**python3.7 --> python3.6**_ [python releases](https://www.python.org/doc/versions/)  
  
  
**[Issue]** Path looking for -   
`'/Users/trionfo1/.virtualenv/kd_local/lib/python3.7/config-3.7m/libpython3.7.a'`  
and actual path in the virtualenv was `/usr/local/Cellar/python/3.7.1/Frameworks/Python.framework/Versions/3.7/lib/python3.7/config-3.7m-darwin`  
  
I got the above error while installing _**uwsgi**_ with following **[Libraries] :**  
  
- Homebrew  
  
- Clang 11  
  
- Mac Os catalina  
  
- python 3.7  
  
- uwsgi 2.0.18  
  
- pip3  
  
- django2.2

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:02 UTC  
> Url: https://github.com/boostorg/build/issues/374#issuecomment-850877319  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
