# #404 - Boost failed to run libs\python\test on x64 and x86 on release and debug configuration with MSVC on windows [Open]

> Username: WickWeiWkSpace  
> Created at: 2022-12-09 09:17:29 UTC  
> Updated at: 2022-12-10 03:47:44 UTC  
> Url: https://github.com/boostorg/python/issues/404  

Hi all,  
  
Boost failed to run libs\python\test on x64 and x86 on release and debug configuration with **MSVC** on windows. it can be reproduced on c368e0b on master branch. Could you please help look at this issue?  
  
**Environment:**  
VS 2019 + Windows Server 2019  
  
  
  
**Repro steps:**  
  
1. git clone https://github.com/boostorg/boost.git F:\gitP  
2. cd F:\gitP\boostorg\boost  
3. .\bootstrap 2>&1  
4. .\b2 headers variant=debug --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64 2>&1  
5. .\b2 variant=debug --build-dir=F:\gitP\boostorg\boost\out\amd64rel address-model=64 2>&1  
6. set _CL_=/D_SILENCE_EXPERIMENTAL_FILESYSTEM_DEPRECATION_WARNING  
7. set OPENSSL_ROOT=F:\tools\OpenSSL\64  
8. set path=F:\tools\OpenSSL\64\bin  
9. set path=C:\Python\Python27  
10. .\b2 -j16 variant=debug --build-dir=F:\gitP\boostorg\boost\out\amd64rel libs\python\test 2>&1  
  
**Error info:**  
[boost_test_debug.log](https://github.com/boostorg/boost/files/10193369/boost_test_debug.log)  
  
	  F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python 2.7\threading-multi\exec-dynamic.run  
		====== BEGIN OUTPUT ======  
	     
		EXIT STATUS: -1073741819   
		====== END OUTPUT ======  
	    
		  set Path=C:\Python\Python27;C:\Python\Python27\libs;F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\build\msvc-14.2\debug\python-2.7\threading-multi;%Path%  
	    
		  set status=0  
		  if %status% NEQ 0 (  
			  echo Skipping test execution due to testing.execute=off  
			  exit 0  
		  )  
		  set PYTHONPATH=C:\Python\Python27/Lib;%PYTHONPATH%  
	  "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.exe"  "libs\python\test\exec.py" > "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.output" 2>&1   
		  set status=%ERRORLEVEL%  
		  echo. >> "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.output"  
		  echo EXIT STATUS: %status% >> "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.output"  
		  if %status% EQU 0 (  
			  copy "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.output" "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.run"  
		  )  
		  set verbose=0  
		  if %status% NEQ 0 (  
			  set verbose=1  
		  )  
		  if %verbose% EQU 1 (  
			  echo ====== BEGIN OUTPUT ======  
			  type "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.output"  
			  echo ====== END OUTPUT ======  
		  )  
		  exit %status%  
	    
	  ...failed testing.capture-output F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec-dynamic.test\msvc-14.2\debug\python-2.7\threading-multi\exec-dynamic.run...  
  
	testing.capture-output F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.run  
	====== BEGIN OUTPUT ======  
	   
	EXIT STATUS: -1073741819   
	====== END OUTPUT ======  
  
		set Path=C:\Python\Python27;C:\Python\Python27\libs;%Path%  
  
		set status=0  
		if %status% NEQ 0 (  
			echo Skipping test execution due to testing.execute=off  
			exit 0  
		)  
		set PYTHONPATH=C:\Python\Python27/Lib;%PYTHONPATH%  
	 "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.exe"  "libs\python\test\exec.py" > "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.output" 2>&1   
		set status=%ERRORLEVEL%  
		echo. >> "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.output"  
		echo EXIT STATUS: %status% >> "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.output"  
		if %status% EQU 0 (  
			copy "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.output" "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.run"  
		)  
		set verbose=0  
		if %status% NEQ 0 (  
			set verbose=1  
		)  
		if %verbose% EQU 1 (  
			echo ====== BEGIN OUTPUT ======  
			type "F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.output"  
			echo ====== END OUTPUT ======  
		)  
		exit %status%  
  
	...failed testing.capture-output F:\gitP\boostorg\boost\out\amd64rel\boost\bin.v2\libs\python\test\exec.test\msvc-14.2\debug\python-2.7\threading-multi\exec.run...
