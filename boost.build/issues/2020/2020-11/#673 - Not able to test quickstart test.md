# #673 - Not able to test quickstart test [Open]

> Username: h-ravichandra  
> Created at: 2020-11-24 10:36:23 UTC  
> Updated at: 2021-05-29 16:23:11 UTC  
> Url: https://github.com/boostorg/build/issues/673  

Below are the steps followed  
  
1. downloaded and unzip boost_1_73_0 and kept it in C:\Program Files\boost\ path  
2. excecutes following commands  
    - bootstrap.bat  
    - .\b2.exe  
     
    After these bjam exe created in C:\Program Files\boost\boost_1_73_0\tools\build\src\engine foldedr, so inorder to   
    invoke bjam i have added it to environment path  
3. RUN  
   cd C:\Program Files\boost\boost_1_73_0\libs\python\example\tutorial  
   bjam toolset=msvc --verbose-test test  
  
After the above commands and steps followed facing the below issue  
  
	'''  
	warning: no Python configured in user-config.jam  
	warning: will use default configuration  
	...found 21 targets...  
	...updating 5 targets...  
	compile-c-c++ hello.obj  
	hello.cpp  
	hello.cpp(7): fatal error C1083: Cannot open include file: 'boost/python/module.hpp': No such file or directory  
  
	    call "..\bin\standalone\msvc\msvc-14.2\msvc-setup.bat"  >nul  
	 cl /Zm800 -nologo @"hello.obj.rsp"  
  
	...failed compile-c-c++ hello.obj...  
	...skipped <p.>hello_ext.pyd for lack of <p.>hello.obj...  
	...skipped <p.>hello for lack of <p.>hello_ext.pyd...  
	...skipped <p.>hello_ext.pdb for lack of <p.>hello.obj...  
	...failed updating 1 target...  
	...skipped 4 targets...  
	'''  
  
And didn't find user-config.jam in windows home dir so where i can find the permanent location for that?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:41 UTC  
> Url: https://github.com/boostorg/build/issues/673#issuecomment-850859644  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
