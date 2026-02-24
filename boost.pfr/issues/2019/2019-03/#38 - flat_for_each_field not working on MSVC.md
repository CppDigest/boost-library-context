# #38 - flat_for_each_field not working on MSVC [Closed]

> Username: janwilmans  
> Created at: 2019-03-25 14:34:28 UTC  
> Updated at: 2020-10-21 08:17:04 UTC  
> Closed at: 2020-10-21 08:17:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/38  

```  
#include <boost/pfr/flat/core.hpp>  
  
int main() {  
    struct nested { int i; short data[3]; };  
    struct foo { int i; nested n; };  
    std::size_t sum = 0;  
    boost::pfr::flat_for_each_field(foo{1, {2, {3, 4, 5}}}, [&sum](auto v) {  
        sum += v;  
    });  
}  
```  
  
This example does not compile, even with **/std:c++17** and **without /permissive-**.  
  
**Reproduce**: just paste the code in a new console project, set it to c++17 and conformance mode to 'no'  
  
![image](https://user-images.githubusercontent.com/5933444/54928085-a99b2200-4f13-11e9-89d9-cb0e8f27e16f.png)  
  
  
This is the error message:  
```  
1>    C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.16.27023\bin\HostX86\x64\CL.exe /c /Imagic_get\include /Zi /nologo /W3 /WX- /diagnostics:classic /sdl /O2 /Oi /GL /D NDEBUG /D _CONSOLE /D _UNICODE /D UNICODE /Gm- /EHsc /MD /GS /Gy /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /std:c++17 /Yu"pch.h" /Fp"x64\Release\ConsoleApplication10.pch" /Fo"x64\Release\\" /Fd"x64\Release\vc141.pdb" /Gd /TP /FC /errorReport:prompt ConsoleApplication10.cpp  
1>    Tracking command:  
1>    C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\MSBuild\15.0\Bin\Tracker.exe /d "C:\Program Files (x86)\MSBuild\15.0\FileTracker\FileTracker32.dll" /i "C:\Users\Jan Wilmans\source\repos\ConsoleApplication10\ConsoleApplication10\x64\Release\ConsoleA.D2A74CDF.tlog" /r "C:\USERS\JAN WILMANS\SOURCE\REPOS\CONSOLEAPPLICATION10\CONSOLEAPPLICATION10\CONSOLEAPPLICATION10.CPP" /b MSBuildConsole_CancelEvent3fe69d296dda41e989270a8edadfde3e  /c "C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.16.27023\bin\HostX86\x64\CL.exe"  /c /Imagic_get\include /Zi /nologo /W3 /WX- /diagnostics:classic /sdl /O2 /Oi /GL /D NDEBUG /D _CONSOLE /D _UNICODE /D UNICODE /Gm- /EHsc /MD /GS /Gy /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /std:c++17 /Yu"pch.h" /Fp"x64\Release\ConsoleApplication10.pch" /Fo"x64\Release\\" /Fd"x64\Release\vc141.pdb" /Gd /TP /FC /errorReport:prompt ConsoleApplication10.cpp  
1>    ConsoleApplication10.cpp  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(103): error C3779: 'boost::pfr::detail::loophole': a function that returns 'auto' cannot be used before it is defined  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(59): note: see declaration of 'boost::pfr::detail::loophole'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(162): note: see reference to class template instantiation 'boost::pfr::detail::loophole_type_list_lref<T,U>' being compiled  
1>            with  
1>            [  
1>                T=type,  
1>                U=indexes  
1>            ]  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(237): note: see reference to function template instantiation 'auto boost::pfr::detail::tie_as_tuple_loophole_impl<T>(T &) noexcept' being compiled  
1>            with  
1>            [  
1>                T=main::foo  
1>            ]  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(133): note: see reference to function template instantiation 'auto boost::pfr::detail::tie_as_flat_tuple<T>(T &)' being compiled  
1>            with  
1>            [  
1>                T=main::foo  
1>            ]  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\consoleapplication10.cpp(11): note: see reference to function template instantiation 'void boost::pfr::flat_for_each_field<main::foo,main::<lambda_611397c7fcdc2babe31551a0ec0cc907>>(T &&,F &&)' being compiled  
1>            with  
1>            [  
1>                T=main::foo,  
1>                F=main::<lambda_611397c7fcdc2babe31551a0ec0cc907>  
1>            ]  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(128): error C3779: 'boost::pfr::detail::loophole': a function that returns 'auto' cannot be used before it is defined  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(59): note: see declaration of 'boost::pfr::detail::loophole'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(166): error C3203: 'tuple': unspecialized class template can't be used as a template argument for template parameter 'S', expected a real type  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\offset_based_getter.hpp(63): error C2338: ====================> Boost.PFR: Member sequence does not indicate correct size for struct type!  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(166): note: see reference to class template instantiation 'boost::pfr::detail::offset_based_getter<type,int>' being compiled  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(168): error C2955: 'boost::pfr::detail::sequence_tuple::tuple': use of class template requires template argument list  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\sequence_tuple.hpp(78): note: see declaration of 'boost::pfr::detail::sequence_tuple::tuple'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(168): error C2512: 'std::integral_constant<size_t,Index>': no appropriate default constructor available  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(168): note: The target type has no constructors  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(164): error C2672: 'boost::pfr::detail::make_flat_tuple_of_references': no matching overloaded function found  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(169): error C2780: 'auto boost::pfr::detail::make_flat_tuple_of_references(TupleOrUserType &,const Getter &,std::integral_constant<size_t,Index>,std::integral_constant<size_t,1>) noexcept': expects 4 arguments - 3 provided  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\make_flat_tuple_of_references.hpp(40): note: see declaration of 'boost::pfr::detail::make_flat_tuple_of_references'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(169): error C2780: 'boost::pfr::detail::sequence_tuple::tuple<> boost::pfr::detail::make_flat_tuple_of_references(TupleOrUserType &,const Getter &,std::integral_constant<size_t,Index>,std::integral_constant<size_t,0>) noexcept': expects 4 arguments - 3 provided  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\make_flat_tuple_of_references.hpp(37): note: see declaration of 'boost::pfr::detail::make_flat_tuple_of_references'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(169): error C2780: 'auto boost::pfr::detail::make_flat_tuple_of_references(TupleOrUserType &,const Getter &,std::integral_constant<size_t,Index>,std::integral_constant<size_t,Index>) noexcept': expects 4 arguments - 3 provided  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\make_flat_tuple_of_references.hpp(34): note: see declaration of 'boost::pfr::detail::make_flat_tuple_of_references'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(236): error C2672: 'boost::pfr::detail::tie_as_tuple_recursively': no matching overloaded function found  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(238): error C2893: Failed to specialize function template 'auto boost::pfr::detail::tie_as_tuple_recursively(T &&) noexcept'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(238): note: With the following template arguments:  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(238): note: 'T=void'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C3536: 'rec_tuples': cannot be used before it is initialized  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2651: 'int': left of '::' must be a class, struct or union  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2062: type 'unknown-type' unexpected  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2039: 'size_v': is not a member of '`global namespace''  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2143: syntax error: missing ';' before '{'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(242): error C2059: syntax error: ')'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(133): error C3313: 'tup': variable cannot have the type 'void'  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(135): error C3536: 'tup': cannot be used before it is initialized  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(26): error C2651: 'void': left of '::' must be a class, struct or union  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): note: see reference to alias template instantiation 'boost::pfr::flat_tuple_size<main::foo>' being compiled  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(137): note: see reference to variable template 'const size_t flat_tuple_size_v<`main'::`2'::foo>' being compiled  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(26): error C2062: type 'unknown-type' unexpected  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(26): error C2039: 'size_v': is not a member of '`global namespace''  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): error C2938: 'boost::pfr::flat_tuple_size<main::foo>' : Failed to specialize alias template  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): error C2062: type 'unknown-type' unexpected  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): error C2039: 'value': is not a member of '`global namespace''  
1>    c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(137): error C3376: 'boost::pfr::flat_tuple_size_v': only static data member templates are allowed  
1>    The command exited with code 2.  
1>  Done executing task "CL" -- FAILED.  
1>Done building target "ClCompile" in project "ConsoleApplication10.vcxproj" -- FAILED.  
1>  
1>Done building project "ConsoleApplication10.vcxproj" -- FAILED.  
1>  
1>Build FAILED.  
1>  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(103): error C3779: 'boost::pfr::detail::loophole': a function that returns 'auto' cannot be used before it is defined  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(128): error C3779: 'boost::pfr::detail::loophole': a function that returns 'auto' cannot be used before it is defined  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(166): error C3203: 'tuple': unspecialized class template can't be used as a template argument for template parameter 'S', expected a real type  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\offset_based_getter.hpp(63): error C2338: ====================> Boost.PFR: Member sequence does not indicate correct size for struct type!  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(168): error C2955: 'boost::pfr::detail::sequence_tuple::tuple': use of class template requires template argument list  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(168): error C2512: 'std::integral_constant<size_t,Index>': no appropriate default constructor available  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(164): error C2672: 'boost::pfr::detail::make_flat_tuple_of_references': no matching overloaded function found  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(169): error C2780: 'auto boost::pfr::detail::make_flat_tuple_of_references(TupleOrUserType &,const Getter &,std::integral_constant<size_t,Index>,std::integral_constant<size_t,1>) noexcept': expects 4 arguments - 3 provided  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(169): error C2780: 'boost::pfr::detail::sequence_tuple::tuple<> boost::pfr::detail::make_flat_tuple_of_references(TupleOrUserType &,const Getter &,std::integral_constant<size_t,Index>,std::integral_constant<size_t,0>) noexcept': expects 4 arguments - 3 provided  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(169): error C2780: 'auto boost::pfr::detail::make_flat_tuple_of_references(TupleOrUserType &,const Getter &,std::integral_constant<size_t,Index>,std::integral_constant<size_t,Index>) noexcept': expects 4 arguments - 3 provided  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(236): error C2672: 'boost::pfr::detail::tie_as_tuple_recursively': no matching overloaded function found  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(238): error C2893: Failed to specialize function template 'auto boost::pfr::detail::tie_as_tuple_recursively(T &&) noexcept'  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C3536: 'rec_tuples': cannot be used before it is initialized  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2651: 'int': left of '::' must be a class, struct or union  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2062: type 'unknown-type' unexpected  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2039: 'size_v': is not a member of '`global namespace''  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(241): error C2143: syntax error: missing ';' before '{'  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\detail\core14_loophole.hpp(242): error C2059: syntax error: ')'  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(133): error C3313: 'tup': variable cannot have the type 'void'  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(135): error C3536: 'tup': cannot be used before it is initialized  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(26): error C2651: 'void': left of '::' must be a class, struct or union  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(26): error C2062: type 'unknown-type' unexpected  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(26): error C2039: 'size_v': is not a member of '`global namespace''  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): error C2938: 'boost::pfr::flat_tuple_size<main::foo>' : Failed to specialize alias template  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): error C2062: type 'unknown-type' unexpected  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\tuple_size.hpp(36): error C2039: 'value': is not a member of '`global namespace''  
1>c:\users\jan wilmans\source\repos\consoleapplication10\consoleapplication10\magic_get\include\boost\pfr\flat\core.hpp(137): error C3376: 'boost::pfr::flat_tuple_size_v': only static data member templates are allowed  
1>    0 Warning(s)  
1>    27 Error(s)  
1>  
1>Time Elapsed 00:00:00.52  
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========  
```

---

## Comment 1

> Username: MKlimenko  
> Created at: 2019-04-20 07:49:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/38#issuecomment-485067536  

@janwilmans, according to the [documentation](http://apolukhin.github.io/magic_get/boost_precise_and_flat_reflectio/tutorial.html):  
> MSVC currently supports only **precise** functions and only in /std:c++latest or /std:c++17 modes.

---

## Comment 2

> Username: janwilmans  
> Created at: 2019-04-20 08:15:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/38#issuecomment-485069054  

I'm not sure what that means? what are precise functions and get we work around them?

---

## Comment 3

> Username: MKlimenko  
> Created at: 2019-04-20 08:42:49 UTC  
> Updated at: 2019-04-20 08:50:12 UTC  
> Url: https://github.com/boostorg/pfr/issues/38#issuecomment-485070532  

There's a code example:  
```cpp  
#include <boost/pfr.hpp>  
  
struct nested_t { char c; };  
struct foo_t { int i; nested_t nested; };  
  
static_assert(std::is_same<  
    boost::pfr::flat_tuple_element_t<1, foo_t>, // Flat reflection.  
    char    // `char`, not `nested_t`!  
>::value, "");  
  
// Requires C++17 or Loophole enabled:  
static_assert(std::is_same<  
    boost::pfr::tuple_element_t<1, foo_t>,      // Precise reflection.  
    nested_t  
>::value, "");  
```  
  
In other words, a flat reflection approach unwraps the nested structures, while the precise one accesses the fields as-is. In the example I've provided, the flattened structure will look like this:  
  
```cpp  
struct nested_t { char c; };  
struct foo_t {   
    int i;  
    char c; // from nested_t   
};  
```  
  
Exactly the same will happen to your code:  
  
```cpp  
struct foo {   
    int i;   
    int i_nested;  
    short data_0;  
    short data_1;  
    short data_2;  
};  
```

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-10-21 08:17:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/38#issuecomment-713395048  

During the Boost Formal Review a bunch of changes were requested and applied., including the removal of `flat_` functions. That removes a lot of confusion.  
  
I'll release a 2.0.0 version of the library soon. That version will be in Boost too.
