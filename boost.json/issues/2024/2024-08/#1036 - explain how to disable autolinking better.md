# #1036 - explain how to disable autolinking better [Closed]

> Username: kjgbrt  
> Created at: 2024-08-27 12:58:44 UTC  
> Updated at: 2024-10-10 07:54:38 UTC  
> Closed at: 2024-10-10 07:54:38 UTC  
> Url: https://github.com/boostorg/json/issues/1036  

### Version of Boost  
Version: 1.85.0  
### Steps necessary to reproduce the problem  
Hello,  
1. I download a copy of the entire boost library, 1.85, and unzip it to a folder  
2. I make a new project in Visual Studio, make sure "hello world" compiles, and then add the path to the boost library via Properties >> C/C++ >> General >> Additional Include Directories  
3. I verify that boost can run by running the simple program on the getting started page (https://www.boost.org/doc/libs/1_86_0/more/getting_started/windows.html)  
4. I then run the following code because I want to use json header-only:   
``` cpp  
#define BOOST_JSON_NO_LIB  
#include <iostream>  
#include <boost/json/src.hpp>  
  
using std::cout;  
  
int main() {  
    cout << "hi\n";  
    return 0;  
}  
  
```  
When I try to compile, i get the following error:   
`LNK1104    cannot open file 'libboost_container-vc143-mt-gd-x64-1_85.lib'`  
  
What am I doing wrong here?   
  
### All relevant compiler information  
Windows OS   
VS 2022  
C++14  
Microsoft (R) C/C++ Optimizing Compiler Version 19.40.33811 for x86  
  
  
I'd really appreciate any help here, sorry for the confusion, I'm not an advanced C++ user nor Boost user. Thank you in advance

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-08-27 13:06:19 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2312512201  

Are you sure you don't also link to a pre-built copy of Boost.JSON by accident? Does Visual Studio show you the actual link command?

---

## Comment 2

> Username: kjgbrt  
> Created at: 2024-08-27 13:12:36 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2312527606  

I'm not sure how I'd check what you mean.   
  
output of hitting "build":   
```  
Build started at 15:10...  
1>------ Build started: Project: boost_json, Configuration: Debug x64 ------  
1>main.cpp  
1>LINK : fatal error LNK1104: cannot open file 'libboost_container-vc143-mt-gd-x64-1_85.lib'  
1>Done building project "boost_json.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
========== Build completed at 15:10 and took 01.430 seconds ==========  
```  
That's everything.   
  
Also, I've been going through various online searches, and I'd like to note the following is set: Properties >> Configuration Properties >> C/C++ >> Precompiled Headers >> Precompiled Header = "Not Using Precompiled Headers"

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-08-27 13:24:54 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2312557420  

There's this answer on StackOverflow, that might help you with getting the link command: https://stackoverflow.com/questions/1319972/how-to-show-linker-command-line-in-build-output-in-visual-studio.  
  
Also, is that definitely the entire contents of your project? No other lines in that cpp file, no other cpp files?

---

## Comment 4

> Username: kjgbrt  
> Created at: 2024-08-27 15:04:25 UTC  
> Updated at: 2024-08-27 15:28:59 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2312819248  

See information below. I'm at the very beginning of trying to use Boost.JSON, and I'm testing it out in a clean, empty project. Note, I changed the setting per the question asked in the link, and thus there's now 56 Warnings also popping up. I found* where the build log is exported, it looks like effectively the same output as below.  
  
![image](https://github.com/user-attachments/assets/b33fb608-3de1-4242-bfcf-9522ecb8349d)  
  
the output expanded slightly:   
```  
Build started at 16:15...  
1>------ Build started: Project: boost_json, Configuration: Debug x64 ------  
1>main.cpp  
1>Microsoft (R) Incremental Linker Version 14.40.33811.0  
1>Copyright (C) Microsoft Corporation.  All rights reserved.  
1>"/OUT:C:\XXX\boost_json\x64\Debug\boost_json.exe" /INCREMENTAL "/ILK:boost_json\x64\Debug\boost_json.ilk" kernel32.lib user32.lib gdi32.lib winspool.lib comdlg32.lib advapi32.lib shell32.lib ole32.lib oleaut32.lib uuid.lib odbc32.lib odbccp32.lib /MANIFEST "/MANIFESTUAC:level='asInvoker' uiAccess='false'" /manifest:embed /DEBUG "/PDB:C:\XXX\boost_json\x64\Debug\boost_json.pdb" /SUBSYSTEM:CONSOLE /TLBID:1 /DYNAMICBASE /NXCOMPAT "/IMPLIB:C:\XXX\cpp\boost_json\x64\Debug\boost_json.lib" /MACHINE:X64 boost_json\x64\Debug\main.obj  
1>LINK : fatal error LNK1104: cannot open file 'libboost_container-vc143-mt-gd-x64-1_85.lib'  
1>Done building project "boost_json.vcxproj" -- FAILED.  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
========== Build completed at 16:15 and took 01.399 seconds ==========  
  
```

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-08-28 04:34:08 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2314236844  

As an option, try defining `BOOST_ALL_NO_LIB` rather than `BOOST_JSON_NO_LIB`.

---

## Comment 6

> Username: kjgbrt  
> Created at: 2024-08-28 06:23:52 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2314415587  

ah, that finally worked. thank you very much. could the readme please be updated? additionally, i assume this could potentially interfere with any boost components that do require a library?

---

## Comment 7

> Username: grisumbras  
> Created at: 2024-08-28 07:15:52 UTC  
> Url: https://github.com/boostorg/json/issues/1036#issuecomment-2314503343  

Yeah, I'll amend the README.
