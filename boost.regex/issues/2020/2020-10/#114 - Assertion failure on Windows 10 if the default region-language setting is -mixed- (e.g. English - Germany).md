# #114 - Assertion failure on Windows 10 if the default region/language setting is "mixed" (e.g. English / Germany) [Open]

> Username: nwu-ableton  
> Created at: 2020-10-29 11:42:50 UTC  
> Updated at: 2025-07-10 18:39:31 UTC  
> Url: https://github.com/boostorg/regex/issues/114  

# Summary  
  
The following program aborts with an assertion failure on Windows 10 if the default language/region is set to `English/Germany`:  
  
```cpp  
#include <boost/regex.hpp>  
  
int main()  
{  
  auto regex = boost::regex{""};  
}  
```  
  
Assertion:  
  
```  
Assertion failed: r != 0, file libs\regex\build\..\src\w32_regex_traits.cpp, line 125  
```  
  
The assertion does not happen if the language/region is set to `English/United States`.  
  
Also see [this StackOverflow thread](https://stackoverflow.com/questions/62722948/c-assertion-crash-when-using-boost-regex-lib) for another example of someone encountering this issue.  
  
# Details  
  
## Boost version  
  
Boost version: 1.74.0, as downloaded from [sourceforge](https://sourceforge.net/projects/boost/files/boost-binaries/1.74.0/boost_1_74_0-bin-msvc-all-32-64.7z/download)  
Binaries: `boost_regex-vc142-mt-gd-x64-1_74.lib`/`.dll` from `lib64-msvc-14.2` directory  
  
## Operating system   
  
Edition: Windows 10 Pro for Workstations  
System type: 64-bit operating system  
Version: 1809  
OS build: 17763.1397  
  
## Compiler & flags  
  
Edition: Microsoft Visual Studio Professional 2019  
Version: 16.7.3  
  
Compiler flags/project settings set to the default for a newly created blank C++ project in VS 2019, aside from adding  
the `BOOST_ALL_NO_LIB` define and adding the necessary include path and `lib` file to compile the code.  
  
The resulting compiler command line arguments are:  
  
```  
/JMC /permissive- /GS /W3 /Zc:wchar_t /ZI /Gm- /Od /sdl /Fd"x64\Debug\vc142.pdb" /Zc:inline /fp:precise /D "_DEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /RTC1 /Gd /MDd /FC /Fa"x64\Debug\" /EHsc /nologo /Fo"x64\Debug\" /Fp"x64\Debug\LocaleTest.pch" /diagnostics:column   
```  
  
Linker command line arguments:  
  
```  
/OUT:"C:\Users\nwu\source\repos\LocaleTest\x64\Debug\LocaleTest.exe" /MANIFEST /NXCOMPAT /PDB:"C:\Users\nwu\source\repos\LocaleTest\x64\Debug\LocaleTest.pdb" /DYNAMICBASE "C:\Dev\boost_1_74_0-bin-msvc-all-32-64\boost_1_74_0\lib64-msvc-14.2\boost_regex-vc142-mt-gd-x64-1_74.lib" "kernel32.lib" "user32.lib" "gdi32.lib" "winspool.lib" "comdlg32.lib" "advapi32.lib" "shell32.lib" "ole32.lib" "oleaut32.lib" "uuid.lib" "odbc32.lib" "odbccp32.lib" /DEBUG /MACHINE:X64 /INCREMENTAL /PGD:"C:\Users\nwu\source\repos\LocaleTest\x64\Debug\LocaleTest.pgd" /SUBSYSTEM:CONSOLE /MANIFESTUAC:"level='asInvoker' uiAccess='false'" /ManifestFile:"x64\Debug\LocaleTest.exe.intermediate.manifest" /ERRORREPORT:PROMPT /NOLOGO /TLBID:1   
```  
  
## Language settings  
  
### Problematic settings (assertion triggered)  
  
![image](https://user-images.githubusercontent.com/8982810/97561952-3310b100-19e1-11eb-9304-d8f7462ce88b.png)  
  
### Working settings (no assertion)  
  
![image](https://user-images.githubusercontent.com/8982810/97561902-21c7a480-19e1-11eb-9a25-13b23b301f1b.png)

---

## Comment 1

> Username: pshurgal  
> Created at: 2024-06-25 13:54:45 UTC  
> Url: https://github.com/boostorg/regex/issues/114#issuecomment-2189029201  

I have the same issue with English/Lithuanian language. The function `LCMapStringA` fails with `ERROR_INSUFFICIENT_BUFFER`. If I increase the buffer `m_lower_map` 4 times the function `LCMapStringA` returns number 512

---

## Comment 2

> Username: matbech  
> Created at: 2025-07-10 18:35:33 UTC  
> Updated at: 2025-07-10 18:39:31 UTC  
> Url: https://github.com/boostorg/regex/issues/114#issuecomment-3058513954  

I'm unable to reproduce the assert with the latest regex version on Windows 11 Version 10.0.26120.4520  
  
<img width="1535" height="594" alt="Image" src="https://github.com/user-attachments/assets/a96f8787-56cf-4316-9059-d49ea748bb10" />
