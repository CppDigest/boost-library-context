# #153 - Could not build boost-python extension project with msvc [Open]

> Username: pavel-machyniak  
> Created at: 2016-12-14 14:32:35 UTC  
> Updated at: 2016-12-14 14:32:35 UTC  
> Url: https://github.com/boostorg/build/issues/153  

It seems that at link stage the linker arguments are not correct, please see this issue:  
  
http://stackoverflow.com/questions/29450634/compile-boost-python-tutorial-with-vs-2015-ctp-5-and-python-3-5a-on-windows-10-t  
  
Link command line:  
`link /NOLOGO /INCREMENTAL:NO /DLL /NOENTRY /DEBUG /MACHINE:X86 /MANIFEST /subsystem:console /out:"bin\msvc-14.0\debug\threading-multi\hello_ext.dll" /IMPLIB:"bin\msvc-14.0\debug\threading-multi\hello_ext.pdb" /LIBPATH:"C:\python35a3\libs"   @"bin\msvc-14.0\debug\threading-multi\hello_ext.dll.rsp"`  
  
Note: `/IMPLIB` points incorrectly to pdb file (program database = debug symbols)  
  
Suggested solution fix the build problem, but is it ok?
