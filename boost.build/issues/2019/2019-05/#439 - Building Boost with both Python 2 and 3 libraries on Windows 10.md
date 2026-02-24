# #439 - Building Boost with both Python 2 and 3 libraries on Windows 10 [Open]

> Username: AndroXD  
> Created at: 2019-05-06 10:01:18 UTC  
> Updated at: 2021-05-29 17:22:38 UTC  
> Url: https://github.com/boostorg/build/issues/439  

I'm on Windows 10 x64, I already unpacked "boost_1_69_0.7z" and ran "bootstrap.bat", however, when I compile it with "b2 toolset=msvc-14.16 --build-type=complete --abbreviate-paths architecture=x86 address-model=64 install -j4 --with-python python=2.7 --with-python python=3.6" I get this error message: "error: legal values: "3.6"".  
How do I compile more Python libraries? In case that's not possible, how do I choose which Python version I want to use? In case I cannot choose anything else than "3.6" then why that command line option exists at all if there's no choice?  
Thanks, I hope a solution exists.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-05-19 13:23:19 UTC  
> Url: https://github.com/boostorg/build/issues/439#issuecomment-493757141  

AMDG  
  
On 5/18/19 11:08 PM, AndroXD wrote:  
> I'm on Windows 10 x64, I already unpacked "boost_1_69_0.7z" and ran "bootstrap.bat", however, when I compile it with "b2 toolset=msvc-14.16 --build-type=complete --abbreviate-paths architecture=x86 address-model=64 install -j4 --with-python python=2.7 --with-python python=3.6" I get this error message: "error: legal values: "3.6"".  
> How do I compile more Python libraries? In case that's not possible, how do I choose which Python version I want to use? In case I cannot choose anything else than "3.6" then why that command line option exists at all if there's no choice?  
> Thanks, I hope a solution exists.  
>   
  
You need to configure both versions in  
project-config.jam (or user-config.jam).  
The default setup for python only finds  
a single version.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: cspanier  
> Created at: 2020-05-28 11:50:51 UTC  
> Updated at: 2020-05-28 11:58:12 UTC  
> Url: https://github.com/boostorg/build/issues/439#issuecomment-635291525  

I have the same issue with Boost 1.73 and tracked down the problem to a bug in `src/tools/python.jam`, where `local rule configure` prematurely selects a Python interpreter without checking for an explicitly selected version (using "python=X.Y" command line argument).  
  
Context:  
Windows 10 x64  
Python2.7 installed in C:\Python27  
Python3.8 installed in C;\Python38  
The PATH environment variable first lists Python 3.8, then 2.7: `PATH=(...);C:\Python38\Scripts\;C:\Python38\;C:\Python27\Scripts\;C:\Python27\;(...)`  
  
A `user-config.jam` in Boost's root folder (no other user-config in any other folder on my system) containing:  
```  
using python : 2.7 : C:/Python27 ;  
using python : 3.8 : C:/Python38 ;  
```  
I'm calling Boost build using the following arguments:  
```  
./b2  
	--debug-configuration  
	--layout=versioned  
	--build-type=complete  
	--without-mpi  
	--without-graph_parallel  
	python=2.7  
	toolset=msvc-14.2  
	architecture=x86  
	address-model=64  
	binary-format=pe  
	threading=multi  
	link=static,shared  
	define=BOOST_USE_WINAPI_VERSION=0x0A00  
	-sZLIB_INCLUDE=C:\\3rdparty\\x86_64-windows-vc142/include  
	-sZLIB_LIBPATH=C:\\3rdparty\\x86_64-windows-vc142/lib  
	-sBZIP2_INCLUDE=C:\\3rdparty\\x86_64-windows-vc142/include  
	-sBZIP2_LIBPATH=C:\\3rdparty\\x86_64-windows-vc142/lib  
	-sICU_PATH=C:\\3rdparty\\x86_64-windows-vc142  
	--prefix=C:\\3rdparty\\x86_64-windows-vc142  
	install  
	-j32  
```  
  
The (relevant) output I get is this:  
```  
...  
notice: [python-cfg] Configuring python...  
notice: [python-cfg] Registry indicates Python 2.7 installed at "C:\Python27\"  
notice: [python-cfg] Checking interpreter command "python"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\Python38\python.exe" 2>&1'  
notice: [python-cfg] running command 'python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "python"  
notice: [python-cfg]   include path: "C:\Python38\Include"  
notice: [python-cfg]   library path: "C:\Python38\libs"  
notice: [python-cfg]   DLL search path: "C:\Python38"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command 'python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy disabled. Reason:  
notice: [python-cfg]   python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())" aborted with  
notice: [python-cfg]   Traceback (most recent call last):  
  File "<string>", line 1, in <module>  
ModuleNotFoundError: No module named 'numpy'  
C:/3rdparty/packages/boost_1_73_0_orig/tools/build/src/build\feature.jam:491: in feature.validate-value-string from module feature  
error: "2.7" is not a known value of feature <python>  
error: legal values: "3.8"  
```  
  
Adding a few extra lines debug output in `src/tools/python.jam` I noticed that on line 724 `cmds-to-try = [ candidate-interpreters $(version) : $(prefix) : $(target-os) ] ;` gets assigned `python C:\Python38\python C:\Python27\python`. This list is tested one by one on line 763 until the first command is accepted. This test seem to ignore my command line argument `python=2.7`, so the first Python interpreter on Path (which happens to be the 3.8 one) is selected. Later an error is thrown stating "2.7" is not a known value and only "3.8" is accepted.  
  
I've added an extra test on line 785 to only accept selected Python versions (if one is explicitly selected) like so:  
```  
-                if $(cmd)  
+                requested-version = [ MATCH python=(.*) : [ modules.peek : ARGV ] ] ;  
+                if $(cmd) && ( ! ( $(requested-version) ) || ( $(sys.version) = $(requested-version) ) )  
```  
and now it is working as expected and as described in the documentation:  
  
```  
...  
notice: [python-cfg] Configuring python...  
notice: [python-cfg] Registry indicates Python 2.7 installed at "C:\Python27\"  
notice: [python-cfg] Checking interpreter command "python"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\Python38\python.exe" 2>&1'  
notice: [python-cfg] running command 'python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] Checking interpreter command "C:\Python27\python"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\Python27\python.exe" 2>&1'  
notice: [python-cfg] running command 'C:\Python27\python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "C:\Python27\python"  
notice: [python-cfg]   include path: "C:\Python27\Include"  
notice: [python-cfg]   library path: "C:\Python27\libs"  
notice: [python-cfg]   DLL search path: "C:\Python27"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command 'C:\Python27\python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy disabled. Reason:  
notice: [python-cfg]   C:\Python27\python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())" aborted with  
notice: [python-cfg]   Traceback (most recent call last):  
  File "<string>", line 1, in <module>  
ImportError: No module named numpy  
Performing configuration checks  
  
    - default address-model    : 32-bit  
    - default architecture     : x86  
...  
```  
  
Am I missing something or is this indeed a bug?

---

## Comment 3

> Username: cspanier  
> Created at: 2020-05-28 11:56:26 UTC  
> Url: https://github.com/boostorg/build/issues/439#issuecomment-635293783  

Another note: `src/tools/python.jam` line 396 builds a list of versions to search for in Windows registry:  
```  
# Build a list of versions from 3.4 down to 1.5. Because bjam can not enumerate  
# registry sub-keys, we have no way of finding a version with a 2-digit minor  
# version, e.g. 2.10 -- let us hope that never happens.  
#  
.version-countdown = ;  
for local v in [ numbers.range 15 34 ]  
{  
    .version-countdown = [ SUBST $(v) (.)(.*) $1.$2 ] $(.version-countdown) ;  
}  
```  
This code hasn't been updated in a while, because meanwhile we've reached Python 3.8. Changing the upper bound from 34 to 38 (or 39) successfully finds the 3.8 interpreter, but didn't solve the error described above.  
```  
notice: [python-cfg] Registry indicates Python 3.8 installed at "C:\Python38\"  
notice: [python-cfg] Registry indicates Python 2.7 installed at "C:\Python27\"  
```  
Nevertheless, this loop should also be updated.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:08 UTC  
> Url: https://github.com/boostorg/build/issues/439#issuecomment-850868279  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
