# #400 - `qi_repo_keywords` test fails due to msvc-14.1 codegen bug [Closed]

> Username: Kojoley  
> Created at: 2018-09-26 11:12:13 UTC  
> Updated at: 2025-05-10 01:52:34 UTC  
> Closed at: 2025-05-10 01:52:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/400  

`qi_repo_keywords` test fails because of codegen bug introduced in the latest Visual Studio versions. I did find the exact version where it started to fail, but it looks like the last good version should be 15.8.2.  
  
The issue was reported to Microsoft. <sup>[\[link\]](https://developercommunity.visualstudio.com/content/problem/342397/x64-optimization-codgen-bug-in-latest-compiler-ver.html)</sup>  
  
<details>  
  <summary>b2 output</summary>  
  
```  
testing.capture-output bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.run  
====== BEGIN OUTPUT ======  
libs\spirit\repository\test\qi\keywords.cpp(86): test 'test("a=a c=1", kwd("a")[ '=' > char_] / kwd("b")[ '=' > char_] / kwd("c")['=' > int_], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(91): test 'test("a=a b=b c=1", kwd("a",1)[ '=' > char_] / kwd("b")[ '=' > char_] / kwd("c")['=' > int_], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(92): test 'test("a=a b=c b=e c=1", kwd("a",1)[ '=' > char_] / kwd("b",2)[ '=' > char_] / kwd("c")['=' > int_], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(96): test 'test("a=f b=c b=e c=1", kwd("a",1,2)[ '=' > char_] / kwd("b",0,2)[ '=' > char_] / kwd("c",1,2)['=' > int_], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(98): test 'test("a=g a=f b=c b=e c=1", kwd("a",1,2)[ '=' > char_] / kwd("b",0,2)[ '=' > char_] / kwd("c",1,2)['=' > int_], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(102): test 'test("a=f b=c b=e c=1", kwd("a",1,inf)[ '=' > char_] / kwd("b",0,inf)[ '=' > char_] / kwd("c",1,inf)['=' > int_], space )' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(104): test 'test("a=f a=f a=g b=c b=e c=1 a=e", kwd("a",1,inf)[ '=' > char_] / kwd("b",0,inf)[ '=' > char_] / kwd("c",1,inf)['=' > int_], space )' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(134): test 'test("B=a c=1 a=E", no_case[kwd("a")[ "=E" ] / kwd("b")[ '=' > char_] / kwd("c")['=' > int_]], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(135): test 'test("B=a c=1 a=e", no_case[kwd("a")[ "=E" ] / kwd("b")[ '=' > char_] / kwd("c")['=' > int_]], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(137): test 'test("b=a c=1 A=E", no_case[kwd("a")[ '=' > char_]] / kwd("b")[ '=' > char_] / kwd("c")['=' > int_], space)' failed in function 'int __cdecl main(void)'  
libs\spirit\repository\test\qi\keywords.cpp(139): test 'test("A=a c=1 a=E", ikwd("a")[ '=' > char_] / kwd("b")[ '=' > char_] / kwd("c")['=' > int_], space)' failed in function 'int __cdecl main(void)'  
   
EXIT STATUS: 3   
====== END OUTPUT ======  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.exe"   > "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.output"  
    echo EXIT STATUS: %status% >> "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.output"  
    if %status% EQU 0 (  
        copy "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.output" "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output bin.v2\libs\spirit\repository\test\qi_repo_keywords.test\msvc-14.1\release\address-model-64\threading-multi\qi_repo_keywords.run...  
```  
  
</details>  
  
Debug  
---  
  
I have succeeded to identify a place where the problem happens, if you mark `boost::spirit::qi::sequence_base::parse_impl` as noinline and place the breakpoint on `first = iter;` line, you will see this:  
  
```  
+	first	0x000000013f9e13c5 "=1"	const char * &  
+	iter	0x000000013f9e13c7 ""	const char *  
```  
and after stepping over the line:  
```  
+	first	0x000000013f9e13c6 "1"	const char * &  
+	iter	0x000000013f9e13c7 ""	const char *  
```  
  
Of course it is wrong. `first` cannot differ from `iter` after `first = iter` assignment.  
  
At disassembly it looks like the optimizer throws away one of two iterator increments.  
  
Solutions  
---  
  
The only found workarounds are:  
  - Remove inline keyword from `spirit::any_if` function.  
  - Move `first = iter` into the dedicated noinline function.  
  - Copy the iterator before assignment `first = make_copy(iter)`  
    with a helper noinline function.  
  
I do not see any of them as acceptable.

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-13 19:35:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/400#issuecomment-429569279  

The regression in MSVC was confirmed and fixed. It is still unclear when it was introduced and which release will have the fix. I am leaving the issue opened to remind myself to revert #402 when updated msvc land on appveyor.

---

## Comment 2

> Username: saki7  
> Created at: 2025-05-10 01:52:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/400#issuecomment-2868215158  

We're no longer using AppVeyor. Closing as stale.
