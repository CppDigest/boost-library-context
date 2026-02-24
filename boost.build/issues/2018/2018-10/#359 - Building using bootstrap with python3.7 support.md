# #359 - Building using bootstrap with python3.7 support [Open]

> Username: DjLegolas  
> Created at: 2018-10-25 22:58:01 UTC  
> Updated at: 2021-06-26 03:09:52 UTC  
> Url: https://github.com/boostorg/build/issues/359  

I'm trying to build b2 with python3.7 support.  
I tried running the command `bootstrap.bat --with-python="C:\Python37-32"` but it fails.  
  
I'm using MSVC 14.1 (Visual Studio 2017 Enterprise), and there are also python 2.7 and 3.6 installed on the system.  
  
In the log file, where are some strange lines:  
```  
...  
C:\Libraries\boost_1_68_0_\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc141 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\Tools\..\..\VC\ " --with-python="C:\Python37-32"   
Python includes: C:\Python37-32\include  
Python includes: C:\Windows\System32\python27.dll  
...found 161 targets...  
...updating 2 targets...  
[COMPILE] bin.ntx86\b2.exe  
cl : Command line warning D9024 : unrecognized source file type 'C:\Windows\System32\python27.dll', object file assumed  
...  
LINK : fatal error LNK1181: cannot open input file 'C:\Windows\System32\python27.dll'  
  
    "cl"  "/Fdbin.ntx86/" "/Fobin.ntx86/" /Febin.ntx86\b2.exe "-DNDEBUG" "-DOPT_HEADER_CACHE_EXT" "-DOPT_GRAPH_DEBUG_EXT" "-DOPT_SEMAPHORE" "-DOPT_AT_FILES" "-DOPT_DEBUG_PROFILE" "-DJAM_DEBUGGER" "-DOPT_FIX_TARGET_VARIABLES_EXT" "-DOPT_IMPROVED_PATIENCE_EXT" "-DNT" "-DYYSTACKSIZE=5000" "-DHAVE_PYTHON" "/nologo" "/GL" "/MT" "/O2" "/Ob2" "/Gy" "/GF" "/GA" "/wd4996" "-IC:\Python37-32\include" "command.c" "compile.c" "constants.c" "debug.c" "debugger.c" "execcmd.c" "frames.c" "function.c" "glob.c" "hash.c" "hcache.c" "headers.c" "hdrmacro.c" "jam.c" "jambase.c" "jamgram.c" "lists.c" "make.c" "make1.c" "mem.c" "object.c" "option.c" "output.c" "parse.c" "pathsys.c" "regexp.c" "rules.c" "scan.c" "search.c" "subst.c" "w32_getreg.c" "timestamp.c" "variable.c" "modules.c" "strings.c" "filesys.c" "builtins.c" "class.c" "cwd.c" "native.c" "md5.c" "modules\set.c" "modules\path.c" "modules\regex.c" "modules\property-set.c" "modules\sequence.c" "modules\order.c" "execnt.c" "filent.c" "pathnt.c" "kernel32.lib" "advapi32.lib" "user32.lib" "C:\Windows\System32\python27.dll"  
  
...failed [COMPILE] bin.ntx86\b2.exe...  
...skipped bjam.exe for lack of b2.exe...  
...failed updating 1 target...  
```  
  
Why does it trying to use `C:\Windows\System32\python27.dll` when the python version is 3.7?  
An I doing something wrong?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:14 UTC  
> Url: https://github.com/boostorg/build/issues/359#issuecomment-868936388  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
